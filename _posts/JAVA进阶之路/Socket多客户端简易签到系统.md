---
title: Socket多客户端简易签到系统
date: 2016-10-12 16:53:51
tags: JAVA进阶之路
categories: JAVA进阶之路
---

## 概述

### 效果

<center>![](http://o99dg8ap9.bkt.clouddn.com/Socket%E5%A4%9A%E5%AE%A2%E6%88%B7%E7%AB%AF%E7%AE%80%E6%98%93%E7%AD%BE%E5%88%B0%E7%B3%BB%E7%BB%9F.png)</center>

### 原理和功能

- 服务器搭建在内网上，通过内网访问实现签到者位置的判断

- 我这个签到系统没有使用数据库，是将每个人的mac地址和姓名存在数组中，每天签

一次，然后每周可以自动发一封签到数据邮件给维护者

- 以后可以实现数据库，然后扩展更多的功能

<!-- more -->


## 服务端代码

### 服务器主类 MyServer

```java
package qiu;

import java.awt.BorderLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.print.Printable;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.ArrayList;
import java.util.Calendar;
import java.util.List;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTextArea;
import javax.swing.JTextField;
/**
 *  服务端
 * */
public class MyServer extends JFrame{
    
	JTextArea jTextArea =null;//用来显示纯文本的单行区域
	JPanel jPanel=null;
	JScrollPane jScrollPane =null;
	static int num=0;
	ServerSocket ss;
	/*************************************************/
	/*****这里存放签到的人的信息,以后可以直接修改*********/
	public String [] name = {"邱承佳","姜伟","邝遥黔","王浩","刁龙","宋艳","曾诚"
			,"文浩轩","陈鑫海","曹兵","江小斐","魏圳辉","李尚","蔡洋","曾玲萍","邱承佳windows",
			"丁涛","余旺林","苏相学","黄立民","邓诗洋","宋璐君"};
	public String [] mac = {
			"c4:54:44:39:e7:d0",//邱承佳
			"9C-5C-8E-1B-73-61",//姜伟
			"50-7B-9D-CF-A5-FD",//邝遥黔
			"1E-E0-10-14-7C-DB",//王浩
			"54-EE-75-86-D8-6A",//刁龙
			"00-AC-45-41-E6-AB",//宋艳
			"34-E6-AD-00-EF-75",//曾诚
			"12-A5-89-3E-43-B3",//文浩轩
			"F0-DE-F1-70-E8-BE",//陈鑫海
			"54-AB-3A-47-16-CC",//曹兵
			"80-56-F2-68-7C-CD",//江小斐
			"50-7B-9D-83-C8-7F",//魏圳辉
			"C8-5B-76-09-4C-F6",//李尚
			"30-3A-64-72-73-6B",//蔡洋
			"3C-97-0E-D3-FA-E9",//曾玲萍
			"08-00-27-D4-59-99",//邱承佳windows
			"C4-8E-8F-A7-3E-FD",//丁涛
			"B8-2A-72-F1-E3-EF",//余旺林
			"40-E2-30-74-16-D2",//苏相学
			"34-DE-1A-A7-45-8C",//黄立民
			"2E-84-DC-91-58-3F",//邓诗洋
			"12-52-CB-77-86-5B"//宋璐君
	};
//签到次数
public int [] time={0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0
			,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
			0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0};
//用来记录当天是否签到
public boolean[] isQianDao={false,false,false,false,false,false,false,false,false,false,false,
			false,false,false,false,false,false,false,false,false,false,false,false,false,false,false
			,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,
			false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,
			false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,
			false,false,false,false,false,false,false,false,false,false,false,false,false,false,false};
	/*************************************************/
	//把信息发给客户端对象
	PrintWriter printWriter =null;
	String firstQianDaoTime="";//记录的开始签到时间
/**
  *  服务端的主函数
  * */
public static void main(String[] args) {
	// TODO Auto-generated method stub
       new MyServer();
}
/**
 *  服务端的构造函数,用来进行初始化
 * */
public MyServer(){
	//这里是对GUI的初始化
	jTextArea = new JTextArea();
	jScrollPane= new JScrollPane(jTextArea);
	jPanel = new JPanel();
		
	//将两个面板添加布局
	this.add(jScrollPane,BorderLayout.CENTER);
	this.add(jPanel,BorderLayout.SOUTH);
		
	this.setSize(800,600);
	this.setTitle("签到服务器");
	this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);//设置退出按钮
	this.setVisible(true);
    this.setResizable(true);
    //获取时间
    firstQianDaoTime=getTime();
        
    new TimeThread(firstQianDaoTime,time,name,isQianDao,jTextArea).start();
    //下面是socket服务器的搭建
    try {
		//服务器监听
       	 ss = new ServerSocket(10124);
		while(true){
			
			new ServerThread(ss.accept(),num,printWriter,jTextArea,
					name,mac,time,isQianDao).start();
		}
		
	} catch (IOException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	}   
    try {
		ss.close();
	} catch (IOException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	}
 }
/**
 * 用来获取当前的时间
 * @return 当前的时间
 */
public String getTime(){
	//可以对每个单独时间域进行修改
	Calendar c = Calendar.getInstance();
	int week = c.get(Calendar.DAY_OF_WEEK);//周几
	int day = c.get(Calendar.DAY_OF_MONTH);//r
	int month = c.get(Calendar.MONTH);//月份
	int hour = c.get(Calendar.HOUR_OF_DAY);//获取小时
	int minute = c.get(Calendar.MINUTE);
	int second = c.get(Calendar.SECOND);
		
	return month+"月"+day+"号-"+"星期"+week+"--"+hour+"时"+minute+"分"+second+"秒";	
   }
	
}
```

- 通过 name 数组记录每个人的姓名，对应的数组 mac 记录每个人的mac地址，添加

数据只要在数组中添加就行，time 用来记录每个人每个星期的签到次数，每周清零，

isQianDao用来记录用户当天是否签到，然后 MyServer() 里面是界面的初始化，每当有

一个用户请求签到的时候新建一个 ServerThread 线程用来处理用户的请求，

TimeThread 线程用来每天数据更新和每周发送一个电子邮件给维护者，ServerSocket 

是服务器监听，用来接收客户端的请求，没有请求则阻塞等待

### 时间线程类 TimeThread

```java
package qiu;

import java.awt.TextArea;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
import java.util.Calendar;
import java.util.List;

import javax.swing.JButton;
import javax.swing.JTextArea;

public class TimeThread extends Thread{
	int [] time;
	boolean []isQianDao;
	String firstQianDaoTime;
	String  [] name;
	JTextArea jTextArea;
	public TimeThread(String firstQianDaoTime ,int[] time, String[] name, boolean[] isQianDao,
			JTextArea jTextArea){
		this.time=time;
		this.firstQianDaoTime=firstQianDaoTime;
		this.isQianDao=isQianDao;
		this.jTextArea = jTextArea;
		this.name=name;
	}
	
	@Override
	public void run(){
		int hourFlag=1;
		int weekFlag=1;
		boolean emailFlag=true;
		while(true){
			if(getHour()==1){
				hourFlag=1;
			}
			
			//当今天过完的时候将签到状态置为false
			if(getHour()==2 && hourFlag==1){
				for(int i=0;i<isQianDao.length;i++){
						isQianDao[i]=false;
				}						
				hourFlag--;
			}
			if(getWeek()==4){
				weekFlag=1;
				emailFlag=true;
			}
			//当今天是周五的时候发送一封邮件，并将所有状态清空
			if(getWeek()==5 && weekFlag==1){
				//获取签到情况的起止时间
				String s="签到开始时间="+firstQianDaoTime+"\r\n"+
				"签到结束时间="+getTimeStr()+"\r\n";
				firstQianDaoTime = getTimeStr();
				for(int i=0;i<name.length;i++){
					s=s+name[i]+"--的签到次数="+time[i]+"\r\n";
				}
				
				String [] emailArr = {
						"2470041229@qq.com",//邱承佳
						"478529861@qq.com",//余旺林
						"14880047@qq.com"};//王宁
				String [] emailName = {"邱承佳","余旺林","王宁"};
				for(int i=0;i<emailArr.length;i++){
					//发送邮件
					JavaEmail email = new JavaEmail(emailArr[i],s,jTextArea,emailName[i],emailFlag);
					emailFlag=false;
					try {
						email.send();
					} catch (Exception e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}	
				}
				for(int i=0;i<isQianDao.length;i++){
					isQianDao[i]=false;
				}
				for(int i=0;i<time.length;i++){
					time[i]=0;
				}
				weekFlag--;
			}
		}	
	}
	/**
	 * 用来获取当前的时间
	 * @return 当前的时间
	 */
	public int getHour(){
		//可以对每个单独时间域进行修改 分钟
		Calendar c = Calendar.getInstance();
		int hour = c.get(Calendar.HOUR_OF_DAY);//获取小时
		int min = c.get(Calendar.MINUTE);
		return hour;	
	   }
	public int getMin(){
		//可以对每个单独时间域进行修改 分钟
		Calendar c = Calendar.getInstance();
		int hour = c.get(Calendar.HOUR_OF_DAY);//获取小时
		int min = c.get(Calendar.MINUTE);
		return min;	
	   }
	public int getWeek(){
		//可以对每个单独时间域进行修改
		Calendar c = Calendar.getInstance();
		int week = c.get(Calendar.DAY_OF_WEEK);//周几
		return week;	
	   }
	
	/**
	 * 用来获取当前的时间
	 * @return 当前的时间
	 */
	public int getTime(){
		//可以对每个单独时间域进行修改
		Calendar c = Calendar.getInstance();
		int week = c.get(Calendar.DAY_OF_WEEK);//周几
		int day = c.get(Calendar.DAY_OF_MONTH);//r
		int month = c.get(Calendar.MONTH);//月份
		int hour = c.get(Calendar.HOUR_OF_DAY);//获取小时
		int minute = c.get(Calendar.MINUTE);
		int second = c.get(Calendar.SECOND);
			
		return week;	
	   }
	/**
	 * 用来获取当前的时间
	 * @return 当前的时间
	 */
	public String getTimeStr(){
		//可以对每个单独时间域进行修改
				Calendar c = Calendar.getInstance();
				int week = c.get(Calendar.DAY_OF_WEEK);//周几
				int day = c.get(Calendar.DAY_OF_MONTH);//r
				int month = c.get(Calendar.MONTH);//月份
				int hour = c.get(Calendar.HOUR_OF_DAY);//获取小时
				int minute = c.get(Calendar.MINUTE);
				int second = c.get(Calendar.SECOND);
					
				return month+"月"+day+"号-"+"星期"+week+"--"+hour+"时"+minute+"分"+second+"秒";	
	   }
	
}
```

- TimeThread 继承了 Thread 类，在服务器主类 MyServer 中调用，用来实现每天

签到状态的更新，以及每周发送一封邮件给维护者


### 邮件发送类 JavaEmail

```java
package qiu;

import java.util.Date;
import java.util.Properties;
import javax.mail.Address;
import javax.mail.Authenticator;
import javax.mail.Message;
import javax.mail.PasswordAuthentication;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;
import javax.swing.JTextArea;
/** *//**
 * 发送普通邮件，接受普通邮件 发送带有附件的邮件，接收带有附件的邮件 发送html形式的邮件，接受html形式的邮件 发送带有图片的邮件等做了一个总结。
 */
public class JavaEmail
{
    // 邮箱服务器
    private String host = "smtp.163.com";
    // 这个是你的邮箱用户名
    private String username = "qiandaoxitong313";
    // 你的邮箱密码
    private String password = "admin313";
    private String mail_head_name = "ACM实验室签到情况";
    private String mail_head_value = "ACM实验室签到情况";
    private String mail_from = "qiandaoxitong313@163.com";
    private String mail_subject = "ACM实验室签到情况统计";
    private String personalName = "ACM实验室签到情况";
    String mail_to,mail_body;//邮件接收者和邮件的内容
    JTextArea jTextArea;
    String emailName;
    boolean emailFlag;
    /**
     * 邮件要发送给谁，和邮件的内容,flag用来标示邮件有没有发送成功
     * @param jTextArea 
     * @param emailName 
     * @param emailFlag 
     * */
    public JavaEmail(String mail_to,String mail_body, JTextArea jTextArea,
    		String emailName, boolean emailFlag){
    	this.mail_to=mail_to;
    	this.emailName = emailName;
    	this.mail_body =mail_body;
    	this.jTextArea=jTextArea;
    	this.emailFlag = emailFlag;
    }
    /** *//**
     * 此段代码用来发送普通电子邮件
     */
    public void send() throws Exception{
        try{
            Properties props = new Properties(); // 获取系统环境
            Authenticator auth = new Email_Autherticator(); // 进行邮件服务器用户认证
            props.put("mail.smtp.host", host);
            props.put("mail.smtp.auth", "true");
            Session session = Session.getDefaultInstance(props, auth);
            // 设置session,和邮件服务器进行通讯。
            MimeMessage message = new MimeMessage(session);
            // message.setContent("foobar, "application/x-foobar"); // 设置邮件格式
            message.setSubject(mail_subject); // 设置邮件主题
            message.setText(mail_body); // 设置邮件正文
            message.setHeader(mail_head_name, mail_head_value); // 设置邮件标题
            message.setSentDate(new Date()); // 设置邮件发送日期
            Address address = new InternetAddress(mail_from, personalName);
            message.setFrom(address); // 设置邮件发送者的地址
            Address toAddress = new InternetAddress(mail_to); // 设置邮件接收方的地址
            message.addRecipient(Message.RecipientType.TO, toAddress);
            Transport.send(message); // 发送邮件
            jTextArea.append("\r\n*****发送邮件给-"+emailName+"-成功*****\r\n");
            if(emailFlag){
            	jTextArea.append("*****邮件内容*****\r\n");
                jTextArea.append(mail_body+"\r\n");
            }            
        } catch (Exception ex){
            ex.printStackTrace();
            jTextArea.append("\r\n*****发送邮件失败*****\r\n");
        }
    }
    /** *//**
     * 用来进行服务器对用户的认证
     */
    public class Email_Autherticator extends Authenticator
    {
        public Email_Autherticator()
        {
            super();
        }
        public Email_Autherticator(String user, String pwd)
        {
            super();
            username = user;
            password = pwd;
        }
        public PasswordAuthentication getPasswordAuthentication()
        {
            return new PasswordAuthentication(username, password);
        }
    }
 
}
```

- 用来发送邮件，没什么好说的

### 服务器线程类 ServerThread

```java
package qiu;

import java.awt.TextArea;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
import java.util.Calendar;
import java.util.List;

import javax.swing.JButton;
import javax.swing.JTextArea;

public class ServerThread extends Thread{
	Socket socket = null;
	int clientNum;
	 PrintWriter printWriter=null;
	 String info;
	JTextArea jTextArea;
	 String [] name ;
	 String [] mac ;
	 int [] time;
	 boolean[] isQianDao;
	
	public ServerThread(Socket socket ,int num,PrintWriter printWriter,JTextArea jTextArea
			, String[] name,String[] mac, int[] time, boolean[] isQianDao){
		this.socket = socket;
		clientNum = num + 1;
		this.printWriter = printWriter;
		this.jTextArea = jTextArea;
		this.name=name;
		this.mac=mac;
		this.time=time;
		this.isQianDao=isQianDao;
	}
	@Override
	public void run() {
		//获得客户端发送过来的数据的流
       	try {
			BufferedReader br = new BufferedReader
					(new InputStreamReader(socket.getInputStream()));
			printWriter = new PrintWriter(socket.getOutputStream(),true);
			//读取从客户端发送过来的信息
	       	while(true){
	      		 info = br.readLine();
	      		 if(info!=null){
	      			 //数组中存在此mac地址
	      			 int result = isMac(info);
	      			 if(result!=-1){
	      				 //表示该用户已经签到
	      				 if(isQianDao[result]==true){
	      					printWriter.println("-1");//不能重复签到
	      				 }else{
	      					 isQianDao[result]=true;
	      					 time[result]++;
	      					jTextArea.append("\r\n\n*****"+name[result]+"-签到成功*****\r\n"
	      					 +"*****总签到次数="+time[result]+"*****\r\n"+
	      			      			 "*****时间="+getTime()+"*****\r\n"+"*****mac地址="+info+"*****\r\n");
	      					 printWriter.println(time[result]+"");//签到次数
	      					
	      				 }
	      				 
	      			 }else{
	      				jTextArea.append("\r\n\n*****不存在的mac地址*****\r\n"+
	      			 "*****时间="+getTime()+"*****\r\n"+"*****mac地址="+info+"*****\r\n");
			      		printWriter.println("-2");//不存在的mac地址，签到失败
	      			 } 
	       	}
	       }
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	/**
	 * 用来判断mac地址是否存在在数组中
	 * */
	public int isMac(String str){
		int f=-1;
		for(int i=0;i<mac.length;i++){
			if(mac[i].equals(str)){
				f=i;
				return f;
			}
		}
		return f;
	}
	/**
	 * 用来获取当前的时间
	 * @return 当前的时间
	 */
	public String getTime(){
		//可以对每个单独时间域进行修改
		Calendar c = Calendar.getInstance();
		int week = c.get(Calendar.DAY_OF_WEEK);//周几
		int day = c.get(Calendar.DAY_OF_MONTH);//r
		int month = c.get(Calendar.MONTH);//月份
		int hour = c.get(Calendar.HOUR_OF_DAY);//获取小时
		int minute = c.get(Calendar.MINUTE);
		int second = c.get(Calendar.SECOND);
			
		return month+"月"+day+"号-"+"星期"+week+"--"+hour+"时"+minute+"分"+second+"秒";	
	   }
}
```

- 每次服务器监听到有用户请求签到的时候就为用户开辟一个 ServerThread 线程用来

处理用户请求，所谓的多客户端签到系统，多客户就是通过这里来实现的，里面的主要

判断逻辑是通过用户传递过来的 mac 地址来判断，如果服务器中不存在用户的 mac 

地址，就认为是非法请求 ，如果存在再判断是否签到，然后进行处理，最后发送最后

的处理信息传递给客户端


## 客户端 MyClient

```java
package qiu;

import java.awt.BorderLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
import java.net.UnknownHostException;
import java.util.Calendar;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTextArea;
import javax.swing.JTextField;
/**
 * 客户端
 * */
public class MyClient extends JFrame implements ActionListener{
    
	JTextArea jTextArea=null;
	JPanel jPanel=null;
	JScrollPane jScrollPane=null;
	JButton sendButton=null;
	PrintWriter printWriter=null;
	
/**
  *  客户端的主函数
  * */
public static void main(String[] args) {
	// TODO Auto-generated method stub
      new MyClient();
}
/**
 * 客户端构造函数用来初始化
 * */
public MyClient(){
	//GUI初始化
	jTextArea= new JTextArea();
	sendButton= new JButton("签到");
	sendButton.addActionListener(this);
	sendButton.setActionCommand("send");
	jScrollPane=new JScrollPane(jTextArea);
	jPanel=new JPanel();
	jPanel.add(sendButton);
		
	this.add(jScrollPane,BorderLayout.CENTER);
	this.add(jPanel,BorderLayout.SOUTH);
		
	this.setSize(400, 300);
	this.setTitle("签到客户端");
	this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	this.setVisible(true);
    this.setResizable(true);	
        
    //socket通信代码
     try {
    	Socket s= new Socket("127.0.0.1",10124);
		BufferedReader br = new BufferedReader
				(new InputStreamReader(s.getInputStream()));
		printWriter= new PrintWriter(s.getOutputStream(),true);
        while(true){
          	//不停的读取服务器发过来的信息
            String string=br.readLine();
            if(string.equals("-1")){//不能重复签到
            	jTextArea.append("\r\n**********不能重复签到**********\r\n");
            }else if(string.equals("-2")){//没有mac地址
            	jTextArea.append("\r\n**********服务器中不存在您的mac地址,签到失败**********\r\n");
            }else{//签到成功，显示签到次数
            	jTextArea.append("\r\n**********签到成功**********\r\n\n"+
	      			      			 "*****你的总签到次数="+string+"*****\r\n");
            }
            
            }
			
	} catch (UnknownHostException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	} catch (IOException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	}
}
/**
 * 用来获取当前的时间
 * @return 当前的时间
 */
public String getTime(){
	//可以对每个单独时间域进行修改
	Calendar c = Calendar.getInstance();
	int week = c.get(Calendar.DAY_OF_WEEK);//周几
	int day = c.get(Calendar.DAY_OF_MONTH);//r
	int month = c.get(Calendar.MONTH);//月份
	int hour = c.get(Calendar.HOUR_OF_DAY);//获取小时
	int minute = c.get(Calendar.MINUTE);
	int second = c.get(Calendar.SECOND);
		
	return month+"月"+day+"号-"+"星期"+week+"--"+hour+"时"+minute+"分"+second+"秒";	
   }
/**
  * 当button被点击的时候调用
  */
@Override
public void actionPerformed(ActionEvent e) {
	// TODO Auto-generated method stub
	if(sendButton.getActionCommand().equals("send")){
		//将客户端发送的信息发送给服务端
		jTextArea.append("\r\n*****请求签到*****\r\n"+"*****"+getTime()+"*****\r\n");
		//getUnixMACAddress getWindowsMACAddress
		printWriter.println(SystemTool.getUnixMACAddress());
		}
	}
}
```

- 使用的时候注意在构造函数中的服务器 ip 地址和在 actionPerformed() 函数中

得到的是 windows 的 mac 还是 linux 的 mac 地址 

- 客户端中也是先初始化界面，然后监听服务端发送过来的消息


## 工具类 SystemTool

```java
package qiu;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.InetAddress;
import java.net.UnknownHostException;
import java.util.Map;

/**
 * @className: SystemTool
 * @description: 与系统相关的一些常用工具方法. 目前实现的有：获取MAC地址、IP地址、主机名
 * @author: 笑遍世界
 * @createTime: 2010-11-13 下午08:03:44
 */
public class SystemTool {

/**
* 获取当前操作系统名称.
* return 操作系统名称 例如:windows xp,linux 等.
*/
public static String getOSName() {
	return System.getProperty("os.name").toLowerCase();
}
/**
* 获取unix网卡的mac地址.
* 非windows的系统默认调用本方法获取.如果有特殊系统请继续扩充新的取mac地址方法.
* @return mac地址
*/
public static String getUnixMACAddress() {
	String mac = null;
	BufferedReader bufferedReader = null;
	Process process = null;
	try {
			process = Runtime.getRuntime().exec("ifconfig");// linux下的命令，一般取eth0作为本地主网卡 显示信息中包含有mac地址信息
			bufferedReader = new BufferedReader(new InputStreamReader(process
			.getInputStream()));
		String line = null;
		int index = -1;
		while ((line = bufferedReader.readLine()) != null) {
			index = line.toLowerCase().indexOf("硬件地址");// 寻找标示字符串[hwaddr]
			if (index >= 0) {// 找到了
				mac = line.substring(index +"硬件地址".length()+ 1).trim();//  取出mac地址并去除2边空格
				break;
			}
		}
	} catch (IOException e) {
		e.printStackTrace();
	} finally {
		try {
			if (bufferedReader != null) {
				bufferedReader.close();
			}
		} catch (IOException e1) {
			e1.printStackTrace();
		}
		bufferedReader = null;
		process = null;
	}

	return mac;
}

/**
* 获取widnows网卡的mac地址.
* @return mac地址
*/
public static String getWindowsMACAddress() {
	String mac = null;
	BufferedReader bufferedReader = null;
	Process process = null;
	try {
		process = Runtime.getRuntime().exec("ipconfig /all");// windows下的命令，显示信息中包含有mac地址信息
		bufferedReader = new BufferedReader(new InputStreamReader(process
				.getInputStream()));
		String line = null;
		int index = -1;
		while ((line = bufferedReader.readLine()) != null) {
			index = line.toLowerCase().indexOf("物理地址");// 寻找标示字符串[physical address]物理地址
			if (index >= 0) {// 找到了
				index = line.indexOf(":");// 寻找":"的位置
				if (index>=0) {
					mac = line.substring(index + 1).trim();//  取出mac地址并去除2边空格
				}
				break;
			}
		}
	} catch (IOException e) {
		e.printStackTrace();
	} finally {
		try {
			if (bufferedReader != null) {
				bufferedReader.close();
			}
		} catch (IOException e1) {
			e1.printStackTrace();
		}
		bufferedReader = null;
		process = null;
	}

	return mac;
}
/**
* @return  本机主机名
*/
public static String getHostName() {
	InetAddress ia = null;
	try {
		ia = InetAddress.getLocalHost();
	} catch (UnknownHostException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	} 
	if (ia == null ) {
		return "some error..";
	}
	else 
		return ia.getHostName();
}
/**
* @return  本机IP 地址
*/
public static String getIPAddress() {
	InetAddress ia = null;
	try {
		ia = InetAddress.getLocalHost();
	} catch (UnknownHostException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	} 
	if (ia == null ) {
		return "some error..";
	}
	else 
		return ia.getHostAddress();
}

/**
* 测试用的main方法.
* 
* @param argc
*            运行参数.
*/
public static void main(String[] argc) {
	
	 String os = getOSName();
	System.out.println("OS Type:"+os);
	if(os.startsWith("windows")){
		//本地是windows
		String mac = getWindowsMACAddress();
		System.out.println("MAC Address:"+mac);
	}else{
		//本地是非windows系统 一般就是unix
		String mac = getUnixMACAddress();
		System.out.println(mac);
	}
	System.out.println("HostName:"+getHostName());
	System.out.println("IPAddress:"+getIPAddress());
	
	}
}
```

- 用来得到用户的 mac 地址，注意 windows 和 linux 是不一样的

## 源码，客户端，服务器和所需的jar包下载

- [mail.jar](http://oe7guoyyy.bkt.clouddn.com/mail.jar) 邮件发送需要的jar包

- [源码](http://oe7guoyyy.bkt.clouddn.com/qiandao.zip)

- [服务端](http://oe7guoyyy.bkt.clouddn.com/%E7%AD%BE%E5%88%B0%E6%9C%8D%E5%8A%A1%E5%99%A8.jar)

- [windows客户端](http://oe7guoyyy.bkt.clouddn.com/windows%E5%AE%A2%E6%88%B7%E7%AB%AF.jar)

- [linux客户端](http://oe7guoyyy.bkt.clouddn.com/linux%E5%AE%A2%E6%88%B7%E7%AB%AF.jar)

### 服务端的使用方法

- 通过命令

> java -jar 签到服务器.jar

### 客户端的使用方法

- 安装 JDK 之后双击可执行
