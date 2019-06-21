title: ' websocket实现服务器端消息推送'
author: 张翔宇
tags:
  - Websocket
  - ''
categories:
  - JAVA
date: 2019-06-21 15:00:00
---
一、准备
实现主要分为服务器端和客户端，客户端通过websocket与服务器端保持连接，这样服务器就可以向客户端主动发起请求。

二、服务器端
服务器端我是使用的springboot，要是用websocket只需要引入websocket的依赖即可

<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-websocket</artifactId>
	<version>2.0.4.RELEASE</version>
</dependency>
然后主要需要实现两个类，一个是websocket的配置类，还有一个是提供其他服务调用的Service

WebSocketConfig.java

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.server.standard.ServerEndpointExporter;

/**
 * @author chenwenrui
 * @description: WebSocket配置类
 * @date 2019/6/11 18:17
    */
    @Configuration
    public class WebSocketConfig {
    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }
    }
    这个配置文件只起到一个作用就是注册ServerEndpointExporter对象，这个bean会自动注册使用了@ServerEndpoint注解声明的类，比如下面的WebSocketService类

WebSocketService.java

import org.springframework.stereotype.Service;
import javax.websocket.*;
import javax.websocket.server.PathParam;
import javax.websocket.server.ServerEndpoint;
import java.io.IOException;
import java.util.List;
import java.util.concurrent.CopyOnWriteArraySet;
import java.util.stream.Collectors;

/**
 * @author chenwenrui
 * @description: TODO
 * @date 2019/6/11 18:11
    */
    @Service
    @ServerEndpoint(value = "/websocket/{userId}")
    public class WebSocketService {

    /**
     * concurrent包的线程安全Set，用来存放每个客户端对应的WebSocketSet对象。
        */
        private static CopyOnWriteArraySet<WebSocketService> webSocketSet = new CopyOnWriteArraySet<>();

    /**
     * 与某个客户端的连接会话，需要通过它来给客户端发送数据
        */
        private Session session;

    /**
     * 客户端用户id
        */
        private String userId;

    /**
     * 连接建立成功调用的方法
        */
        @OnOpen
        public void onOpen(Session session, @PathParam("userId") String userId) {
        this.session = session;
        this.userId = userId;
        if (userId != null) {
            webSocketSet.add(this);     //加入set中
        }
        }

    /**
     * 连接关闭调用的方法
        */
        @OnClose
        public void onClose() {
        webSocketSet.remove(this);  //从set中删除
        }

    /**
     * 收到客户端消息后调用的方法
        *
     * @param message 客户端发送过来的消息
        */
        @OnMessage
        public void onMessage(String message, Session session) {

    }

    /**
     * 发生错误时调用
        *
     * @param session
     * @param error
        */
        @OnError
        public void onError(Session session, Throwable error) {
        error.printStackTrace();
        }

    /**
     * 向客户端发送消息
        *
     * @param message 消息内容
     * @param userIds 需要发送的客户端用户id列表
        */
        public void sendMessage(String message, List<String> userIds) {
        // 需要发送消息的客户端
        List<WebSocketService> services = webSocketSet.stream()
                .filter(temp -> userIds.contains(temp.userId))
                .collect(Collectors.toList());
        services.forEach(temp -> {
            try {
                temp.session.getBasicRemote().sendText(message);
            } catch (IOException e) {
                e.printStackTrace();
            }
        });
        }
        }
        @ServerEndpoint表示这是一个websocket，因为登录的每一个用户都是一个客户端，所以这里使用用户的id区分不同的客户端，以方便给对应的用户发送消息，每一个客户端发起websocket连接请求的时候，需要把对应对象(WebSocketService，主要是保存userId以及对应客户端的Session)保存到集合里(要供其他服务调用)，用户关闭连接的时候同时需要从集合中去除。

三、客户端
客户端我是使用的vue,只需要在mounted()钩子里面打开websocket的连接即可

mounted(){
	let websocket = new WebSocket(
		"ws://localhost:8089/websocket/" + userId
	);
	 //连接成功建立的回调方法
	websocket.onopen = function(event) {
		console.log("连接成功");
	};
	//接收到消息的回调方法
	websocket.onmessage = function(event) {
		console.log(event);
	};
	 //连接关闭的回调方法
	websocket.onclose = function() {
		console.log("连接关闭");
	};
	//连接发生错误的回调方法
	websocket.onerror = function() {
		console.log("发生错误");
	};
}
在实例化WebSocket对象的时候，添加了当前登录用户的用户id(token也行，只要是能确保当前状态下是唯一的)，用于标记每一个客户端对应的WebSocket连接。
--------------------- 
作者：大锅睿 
来源：CSDN 
原文：https://blog.csdn.net/cwr452829537/article/details/91580331 
