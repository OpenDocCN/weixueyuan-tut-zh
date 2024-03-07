# Spring Boot Admin 监控告警服务

> 原文：[`c.biancheng.net/view/5523.html`](http://c.biancheng.net/view/5523.html)

在微服务架构下，服务的数量少则几十，多则上百，所以对服务的监控必不可少。如果是以前的单体项目，启动了多少个项目是固定的，可以通过第三方监控工具对其进行监控，然后实时告警。

在微服务下，由于服务数量太多，并且可以随时扩展，这个时候第三方的监控功能就不适用了，不过我们可以通过 Spring Boot Admin 连接注册中心来查看服务状态，这个只能在页面查看。

很多时候我们更希望能够自动监控，通过邮件告警，比如发出“某某服务下线了”这样的功能。在 Spring Boot Admin 中其实已经有这样的功能了，我们只需要配置一些邮件的信息就可以使用。

## 邮件警报

引入邮件所需要的依赖，代码如下所示。

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>

然后在配置文件中增加邮件服务器的信息：

spring.mail.host=smtp.qq.com
spring.mail.username=xxx@qq.com
spring.mail.password=qq 邮箱的授权码
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true
spring.mail.properties.mail.smtp.starttls.required=true
# 发送给谁
spring.boot.admin.notify.mail.to=xxx@126.com
# 是谁发送出去的
spring.boot.admin.notify.mail.from=xxx@qq.com

配置好之后就可以收到监控邮件了。

## 自定义钉钉警报

目前很多公司都是用钉钉来办公，通过钉钉可以发送监控消息，非常方便。Spring Boot Admin 中默认是没有钉钉警报这个功能的，我们可以自己去扩展使用钉钉来发送监控信息。

首先我们编写一个发送钉钉消息的工具类，代码如下所示。

```

public class DingDingMessageUtil {
    public static String access_token = "填写你自己申请的 token";

    public static void sendTextMessage(String msg) {
        try {
            Message message = new Message();
            message.setMsgtype("text");
            message.setText(new MessageInfo(msg));
            URL url = new URL("https://oapi.dingtalk.com/robot/send?access_token=" + access_token);
            // 建立 http 连接
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            conn.setDoOutput(true);
            conn.setDoInput(true);
            conn.setUseCaches(false);
            conn.setRequestMethod("POST");
            conn.setRequestProperty("Charset", "UTF-8");
            conn.setRequestProperty("Content-Type", "application/Json; charset=UTF-8");
            conn.connect();
            OutputStream out = conn.getOutputStream();
            String textMessage = JsonUtils.toJson(message);
            byte[] data = textMessage.getBytes();
            out.write(data);
            out.flush();
            out.close();

            InputStream in = conn.getInputStream();
            byte[] data1 = new byte[in.available()];
            in.read(data1);

            System.out.println(new String(data1));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}

class Message {
    private String msgtype;
    private MessageInfo text;

    public String getMsgtype() {
        return msgtype;
    }

    public void setMsgtype(String msgtype) {
        this.msgtype = msgtype;
    }

    public MessageInfo getText() {
        return text;
    }

    public void setText(MessageInfo text) {
        this.text = text;
    }
}

class MessageInfo {
    private String content;

    public MessageInfo(String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
    }
}
```

通过继承 AbstractStatusChangeNotifier 实现钉钉发送机制，代码如下所示。

```

public class DingDingNotifier extends AbstractStatusChangeNotifier {

    public DingDingNotifier(InstanceRepository repository) {
        super(repository);
    }

    @Override
    protected Mono<Void> doNotify(InstanceEvent event, Instance instance) {
        String serviceName = instance.getRegistration().getName();
        String serviceUrl = instance.getRegistration().getServiceUrl();
        String status = instance.getStatusInfo().getStatus();
        Map<String, Object> details = instance.getStatusInfo().getDetails();

        StringBuilder str = new StringBuilder();
        str.append("【" + serviceName + "】");
        str.append("【服务地址】" + serviceUrl);
        str.append("【状态】" + status);
        str.append("【详情】" + JsonUtils.toJson(details));
        return Mono.fromRunnable(() -> {
            DingDingMessageUtil.sendTextMessage(str.toString());
        });
    }
}
```

最后启用 DingDingNotifier 就可以了，代码如下所示。

@Bean
public DingDingNotifier dingDingNotifier() {
    return new DingDingNotifier();
}

目前我们已经配置好了警报功能，当服务上下线的时候就会发送警报，当网络发生波动的时候也有可能会触发警报。当前的警报只会发送一次，也就是说你的服务挂掉之后你会收到一条警报。如果是网络引起的警报也会收到一条警报，这个时候你就无法判断服务是不是真正出问题了。

我们的需求也很简单，当服务真正挂掉的时候，警报可以发送多条，比如每 10 秒发送一条，这样连续性的警报就很容易让维护人员关注和辨别。

可以通过配置 RemindingNotifier 来实现上面的需求：

```

@Primary
@Bean(initMethod = "start", destroyMethod = "stop")
public RemindingNotifier remindingNotifier(InstanceRepository repository) {
    RemindingNotifier notifier = new RemindingNotifier(dingDingNotifier(repo - sitory), repository);
    notifier.setReminderPeriod(Duration.ofSeconds(10));
    notifier.setCheckReminderInverval(Duration.ofSeconds(10));
    return notifier;
}
```

这里设置的时间间隔是 10 秒一次，当服务出问题的时候就会每隔 10 秒发送一次警报，直到服务正常才会停止。

这个功能还会引发一个别的问题，如果你的服务是运行在 Docker 中，采用动态端口的话，当你每次重新发布服务的时候，端口发生变化，Spring Boot Admin 就会认为你的服务一直处于不可用的状态，这点不是特别好解决，笔者能想到的方法是在正常项目发布成功之后把 Spring Boot Admin 重启下，获取最新的服务信息。