---
layout: post
title: "java生成UUID"
date: 2006-07-12 14:52
comments: true
external-url: 
categories: Java
---
**UUID**(Universally Unique Identifier)全局唯一标识符,是指在一台机器上生成的数字，它保证对在同一时空中的所有机器都是唯一的。按照开放软件基金会(OSF)制定的标准计算，用到了以太网卡地址、纳秒级时间、芯片ID码和许多可能的数字。由以下几部分的组合：当前日期和时间(UUID的第一个部分与时间有关，如果你在生成一个UUID之后，过几秒又生成一个UUID，则第一个部分不同，其余相同)，时钟序列，全局唯一的IEEE机器识别号（如果有网卡，从网卡获得，没有网卡以其他方式获得），UUID的唯一缺陷在于生成的结果串会比较长。

在Java中生成UUID主要有以下几种方式:
<!-- more -->

### 1. JDK1.5 ###
如果使用的JDK1.5的话,那么生成UUID变成了一件简单的事,以为JDK实现了UUID: java.util.UUID,直接调用即可.  
	UUID uuid  =  UUID.randomUUID();

### 2. 第三方开源类库(推荐使用): ###
最著名的是 [JUG](http://jug.safehaus.org/) .特点上是: 纯Java实现，开源，LGPL协议。采用了Native的方式产生真正的Uuid.而且提供了不同平台的实现,包括:  

- Linux / x86
- Windows (98, ME, NT, 2K, XP?) / x86
- Solaris / Sparc
- Mac OS X
- FreeBSD / x86

	import  org.doomdark.uuid.UUID;
	import  org.doomdark.uuid.UUIDGenerator;
	
	UUIDGenerator generator  =  UUIDGenerator.getInstance();
	UUID uuid  =  generator.generateRandomBasedUUID();

### 3. Java代码实现: ###
如果你使用JDK1.4或以前版本,又不想加入第三方的类库的话,下面提供了一个纯Java的UUID实现. 不过需要注意的是:**这里产生的可能不是真正的UUID，只不过重复的机会少一些而已**。
	import  java.net.InetAddress;
	import  java.net.UnknownHostException;
	
	/**
	 * UUID（Univeral Unique Identity）类
	 * 
	
	
	 * Title: 生成唯一编码
	 * 
	
	 * 
	
	
	 * Description: 源自于w3c.org <</font>
	http://源自于w3c.org >
	 * 
	 * 
	
	
	 * Copyright: Copyright (c) 2001-2004
	 * 
	
	 * 
	 *  @version  1.0
	  */
	public   final   class  UUID  {
	
	     /**
	     *  @serial  Integer that helps create a unique UID.
	      */
	     private   int  unique;
	
	     /**
	     *  @serial  Long used to record the time. The time will be
	     *         used to create a unique UID.
	      */
	     private   long  time;
	
	     /**
	     * InetAddress to make the UID globally unique
	      */
	     private   static  String address;
	
	     /**
	     * a random number
	      */
	     private   static   int  hostUnique;
	
	     /**
	     * Used for synchronization
	      */
	     private   static  Object mutex;
	
	     private   static   long  lastTime;
	
	     private   static   long  DELAY;
	
	     private   static  String generateNoNetworkID()  {
	        Thread current  =  Thread.currentThread();
	        String nid  =  current.activeCount()  +  System.getProperty( " os.version " )
	                 +  System.getProperty( " user.name  " )
	                 +  System.getProperty( " java.version " );
	        System.out.println( " netWorkId = "   +  nid);
	        MD5 md5  =   new  MD5(nid);
	        md5.processString();
	         return  md5.getStringDigest();
	    }
	
	     static   {
	        hostUnique  =  ( new  Object()).hashCode();
	        mutex  =   new  Object();
	        lastTime  =  System.currentTimeMillis();
	        DELAY  =   10 ;  //  in milliseconds
	         try   {
	            String s  =  InetAddress.getLocalHost().getHostAddress();
	            MD5 md5  =   new  MD5(s);
	            md5.processString();
	            address  =  md5.getStringDigest();
	        }   catch  (UnknownHostException ex)  {
	            address  =  generateNoNetworkID();
	        }
	    }
	
	     public  UUID()  {
	         synchronized  (mutex)  {
	             boolean  done  =   false ;
	             while  ( ! done)  {
	                time  =  System.currentTimeMillis();
	                 if  (time   lastTime  +  DELAY)  {
	                     //  pause for a second to wait for time to change
	                     try   {
	                        Thread.currentThread().sleep(DELAY);
	                    }   catch  (java.lang.InterruptedException e)  {
	                    }   //  ignore exception
	                     continue ;
	                }   else   {
	                    lastTime  =  time;
	                    done  =   true ;
	                }
	            }
	            unique  =  hostUnique;
	        }
	    }
	
	     public  String toString()  {
	         return  Integer.toString(unique,  16 )  +  Long.toString(time,  16 )  +  address;
	    }
	
	     public   boolean  equals(Object obj)  {
	         if  ((obj  !=   null )  &&  (obj  instanceof  UUID))  {
	            UUID uuid  =  (UUID) obj;
	             return  (unique  ==  uuid.unique  &&  time  ==  uuid.time  &&  address
	                    .equals(uuid.address));
	        }   else   {
	             return   false ;
	        }
	    }
	
	     public   static   void  main(String args[])  {
	        System.out.println( new  UUID());
	        System.out.println( new  UUID());
	        System.out.println( new  UUID());
	         long  start  =  System.currentTimeMillis();
	        System.out.println( new  UUID());
	         long  end  =  System.currentTimeMillis();
	        System.out.println((end  -  start));
	        System.out.println( new  UUID().toString().length());
	    }
	
	     /**
	     * 返回最新的UUID号码
	     * 
	     *  @return  String UUID，长50位
	     * 
	      */
	     public   final   static  String getUUID()  {
	        UUID uid  =   new  UUID();
	         return  uid.toString();
	    }
	}

其中使用到**MD5加密算法**,实现代码如下:

	import  java.io.ByteArrayInputStream;
	import  java.io.File;
	import  java.io.FileInputStream;
	import  java.io.IOException;
	import  java.io.InputStream;
	import  java.io.UnsupportedEncodingException;
	
	/**
	 * MD5 加密算法类
	 * 
	 * 
	
	
	 * Description: 源自于w3c.org <</font>
	http://源自于w3c.org >
	 * 
	 * 
	
	
	 * Copyright: Copyright (c) 2001-2004
	 * 
	
	 * 
	 *  @version  1.0
	  */
	public   class  MD5  {
	     private   static   final   int  BUFFER_SIZE  =   1024 ;
	
	     private   static   final   int  S11  =   7 ;
	
	     private   static   final   int  S12  =   12 ;
	
	     private   static   final   int  S13  =   17 ;
	
	     private   static   final   int  S14  =   22 ;
	
	     private   static   final   int  S21  =   5 ;
	
	     private   static   final   int  S22  =   9 ;
	
	     private   static   final   int  S23  =   14 ;
	
	     private   static   final   int  S24  =   20 ;
	
	     private   static   final   int  S31  =   4 ;
	
	     private   static   final   int  S32  =   11 ;
	
	     private   static   final   int  S33  =   16 ;
	
	     private   static   final   int  S34  =   23 ;
	
	     private   static   final   int  S41  =   6 ;
	
	     private   static   final   int  S42  =   10 ;
	
	     private   static   final   int  S43  =   15 ;
	
	     private   static   final   int  S44  =   21 ;
	
	     private   static   byte  padding[]  =   { ( byte )  0x80 , ( byte )  0 , ( byte )  0 ,
	            ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 ,
	            ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 ,
	            ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 ,
	            ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 ,
	            ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 ,
	            ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 ,
	            ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 ,
	            ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 ,
	            ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 ,
	            ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 , ( byte )  0 ,
	            ( byte )  0  } ;
	
	     private  InputStream in  =   null ;
	
	     private   boolean  stringp  =   false ;
	
	     private   int  state[]  =   null ;
	
	     private   long  count  =   0 ;
	
	     private   byte  buffer[]  =   null ;
	
	     private   byte  digest[]  =   null ;
	
	     private   static  String stringify( byte  buf[])  {
	        StringBuffer sb  =   new  StringBuffer( 2   *  buf.length);
	         for  ( int  i  =   0 ; i   buf.length; i ++ )  {
	             int  h  =  (buf[i]  &   0xf0 )  >>   4 ;
	             int  l  =  (buf[i]  &   0x0f );
	            sb.append( new  Character(( char ) ((h  >   9 )  ?   ' a '   +  h  -   10  :  ' 0 '   +  h)));
	            sb.append( new  Character(( char ) ((l  >   9 )  ?   ' a '   +  l  -   10  :  ' 0 '   +  l)));
	        }
	         return  sb.toString();
	    }
	
	     private   final   int  F( int  x,  int  y,  int  z)  {
	         return  ((x  &  y)  |  (( ~ x)  &  z));
	    }
	
	     private   final   int  G( int  x,  int  y,  int  z)  {
	         return  ((x  &  z)  |  (y  &  ( ~ z)));
	    }
	
	     private   final   int  H( int  x,  int  y,  int  z)  {
	         return  (x  ^  y  ^  z);
	    }
	
	     private   final   int  I( int  x,  int  y,  int  z)  {
	         return  (y  ^  (x  |  ( ~ z)));
	    }
	
	     private   final   int  rotate_left( int  x,  int  n)  {
	         return  ((x  <<</span>  n) | (x >>> (32 -  n)));
	    }
	
	     private   final   int  FF( int  a,  int  b,  int  c,  int  d,  int  x,  int  s,  int  ac)  {
	        a  +=  (F(b, c, d)  +  x  +  ac);
	        a  =  rotate_left(a, s);
	        a  +=  b;
	         return  a;
	    }
	
	     private   final   int  GG( int  a,  int  b,  int  c,  int  d,  int  x,  int  s,  int  ac)  {
	        a  +=  (G(b, c, d)  +  x  +  ac);
	        a  =  rotate_left(a, s);
	        a  +=  b;
	         return  a;
	    }
	
	     private   final   int  HH( int  a,  int  b,  int  c,  int  d,  int  x,  int  s,  int  ac)  {
	        a  +=  (H(b, c, d)  +  x  +  ac);
	        a  =  rotate_left(a, s);
	        a  +=  b;
	         return  a;
	    }
	
	     private   final   int  II( int  a,  int  b,  int  c,  int  d,  int  x,  int  s,  int  ac)  {
	        a  +=  (I(b, c, d)  +  x  +  ac);
	        a  =  rotate_left(a, s);
	        a  +=  b;
	         return  a;
	    }
	
	     private   final   void  decode( int  output[],  byte  input[],  int  off,  int  len)  {
	         int  i  =   0 ;
	         int  j  =   0 ;
	         for  (; j   len; i ++ , j  +=   4 )  {
	            output[i]  =  ((( int ) (input[off  +  j]  &   0xff ))
	                     |  ((( int ) (input[off  +  j  +   1 ]  &   0xff ))  <<</span>  8 )
	                     |  ((( int ) (input[off  +  j  +   2 ]  &   0xff ))  <<</span>  16) | (((int ) (input[off
	                     +  j  +   3 ]  &   0xff ))  <<</span>  24 ));
	        }
	    }
	
	     private   final   void  transform( byte  block[],  int  offset)  {
	         int  a  =  state[ 0 ];
	         int  b  =  state[ 1 ];
	         int  c  =  state[ 2 ];
	         int  d  =  state[ 3 ];
	         int  x[]  =   new   int [ 16 ];
	
	        decode(x, block, offset,  64 );
	         /*  Round 1  */
	        a  =  FF(a, b, c, d, x[ 0 ], S11,  0xd76aa478 );  /*  1  */
	        d  =  FF(d, a, b, c, x[ 1 ], S12,  0xe8c7b756 );  /*  2  */
	        c  =  FF(c, d, a, b, x[ 2 ], S13,  0x242070db );  /*  3  */
	        b  =  FF(b, c, d, a, x[ 3 ], S14,  0xc1bdceee );  /*  4  */
	        a  =  FF(a, b, c, d, x[ 4 ], S11,  0xf57c0faf );  /*  5  */
	        d  =  FF(d, a, b, c, x[ 5 ], S12,  0x4787c62a );  /*  6  */
	        c  =  FF(c, d, a, b, x[ 6 ], S13,  0xa8304613 );  /*  7  */
	        b  =  FF(b, c, d, a, x[ 7 ], S14,  0xfd469501 );  /*  8  */
	        a  =  FF(a, b, c, d, x[ 8 ], S11,  0x698098d8 );  /*  9  */
	        d  =  FF(d, a, b, c, x[ 9 ], S12,  0x8b44f7af );  /*  10  */
	        c  =  FF(c, d, a, b, x[ 10 ], S13,  0xffff5bb1 );  /*  11  */
	        b  =  FF(b, c, d, a, x[ 11 ], S14,  0x895cd7be );  /*  12  */
	        a  =  FF(a, b, c, d, x[ 12 ], S11,  0x6b901122 );  /*  13  */
	        d  =  FF(d, a, b, c, x[ 13 ], S12,  0xfd987193 );  /*  14  */
	        c  =  FF(c, d, a, b, x[ 14 ], S13,  0xa679438e );  /*  15  */
	        b  =  FF(b, c, d, a, x[ 15 ], S14,  0x49b40821 );  /*  16  */
	         /*  Round 2  */
	        a  =  GG(a, b, c, d, x[ 1 ], S21,  0xf61e2562 );  /*  17  */
	        d  =  GG(d, a, b, c, x[ 6 ], S22,  0xc040b340 );  /*  18  */
	        c  =  GG(c, d, a, b, x[ 11 ], S23,  0x265e5a51 );  /*  19  */
	        b  =  GG(b, c, d, a, x[ 0 ], S24,  0xe9b6c7aa );  /*  20  */
	        a  =  GG(a, b, c, d, x[ 5 ], S21,  0xd62f105d );  /*  21  */
	        d  =  GG(d, a, b, c, x[ 10 ], S22,  0x2441453 );  /*  22  */
	        c  =  GG(c, d, a, b, x[ 15 ], S23,  0xd8a1e681 );  /*  23  */
	        b  =  GG(b, c, d, a, x[ 4 ], S24,  0xe7d3fbc8 );  /*  24  */
	        a  =  GG(a, b, c, d, x[ 9 ], S21,  0x21e1cde6 );  /*  25  */
	        d  =  GG(d, a, b, c, x[ 14 ], S22,  0xc33707d6 );  /*  26  */
	        c  =  GG(c, d, a, b, x[ 3 ], S23,  0xf4d50d87 );  /*  27  */
	        b  =  GG(b, c, d, a, x[ 8 ], S24,  0x455a14ed );  /*  28  */
	        a  =  GG(a, b, c, d, x[ 13 ], S21,  0xa9e3e905 );  /*  29  */
	        d  =  GG(d, a, b, c, x[ 2 ], S22,  0xfcefa3f8 );  /*  30  */
	        c  =  GG(c, d, a, b, x[ 7 ], S23,  0x676f02d9 );  /*  31  */
	        b  =  GG(b, c, d, a, x[ 12 ], S24,  0x8d2a4c8a );  /*  32  */
	
	         /*  Round 3  */
	        a  =  HH(a, b, c, d, x[ 5 ], S31,  0xfffa3942 );  /*  33  */
	        d  =  HH(d, a, b, c, x[ 8 ], S32,  0x8771f681 );  /*  34  */
	        c  =  HH(c, d, a, b, x[ 11 ], S33,  0x6d9d6122 );  /*  35  */
	        b  =  HH(b, c, d, a, x[ 14 ], S34,  0xfde5380c );  /*  36  */
	        a  =  HH(a, b, c, d, x[ 1 ], S31,  0xa4beea44 );  /*  37  */
	        d  =  HH(d, a, b, c, x[ 4 ], S32,  0x4bdecfa9 );  /*  38  */
	        c  =  HH(c, d, a, b, x[ 7 ], S33,  0xf6bb4b60 );  /*  39  */
	        b  =  HH(b, c, d, a, x[ 10 ], S34,  0xbebfbc70 );  /*  40  */
	        a  =  HH(a, b, c, d, x[ 13 ], S31,  0x289b7ec6 );  /*  41  */
	        d  =  HH(d, a, b, c, x[ 0 ], S32,  0xeaa127fa );  /*  42  */
	        c  =  HH(c, d, a, b, x[ 3 ], S33,  0xd4ef3085 );  /*  43  */
	        b  =  HH(b, c, d, a, x[ 6 ], S34,  0x4881d05 );  /*  44  */
	        a  =  HH(a, b, c, d, x[ 9 ], S31,  0xd9d4d039 );  /*  45  */
	        d  =  HH(d, a, b, c, x[ 12 ], S32,  0xe6db99e5 );  /*  46  */
	        c  =  HH(c, d, a, b, x[ 15 ], S33,  0x1fa27cf8 );  /*  47  */
	        b  =  HH(b, c, d, a, x[ 2 ], S34,  0xc4ac5665 );  /*  48  */
	
	         /*  Round 4  */
	        a  =  II(a, b, c, d, x[ 0 ], S41,  0xf4292244 );  /*  49  */
	        d  =  II(d, a, b, c, x[ 7 ], S42,  0x432aff97 );  /*  50  */
	        c  =  II(c, d, a, b, x[ 14 ], S43,  0xab9423a7 );  /*  51  */
	        b  =  II(b, c, d, a, x[ 5 ], S44,  0xfc93a039 );  /*  52  */
	        a  =  II(a, b, c, d, x[ 12 ], S41,  0x655b59c3 );  /*  53  */
	        d  =  II(d, a, b, c, x[ 3 ], S42,  0x8f0ccc92 );  /*  54  */
	        c  =  II(c, d, a, b, x[ 10 ], S43,  0xffeff47d );  /*  55  */
	        b  =  II(b, c, d, a, x[ 1 ], S44,  0x85845dd1 );  /*  56  */
	        a  =  II(a, b, c, d, x[ 8 ], S41,  0x6fa87e4f );  /*  57  */
	        d  =  II(d, a, b, c, x[ 15 ], S42,  0xfe2ce6e0 );  /*  58  */
	        c  =  II(c, d, a, b, x[ 6 ], S43,  0xa3014314 );  /*  59  */
	        b  =  II(b, c, d, a, x[ 13 ], S44,  0x4e0811a1 );  /*  60  */
	        a  =  II(a, b, c, d, x[ 4 ], S41,  0xf7537e82 );  /*  61  */
	        d  =  II(d, a, b, c, x[ 11 ], S42,  0xbd3af235 );  /*  62  */
	        c  =  II(c, d, a, b, x[ 2 ], S43,  0x2ad7d2bb );  /*  63  */
	        b  =  II(b, c, d, a, x[ 9 ], S44,  0xeb86d391 );  /*  64  */
	
	        state[ 0 ]  +=  a;
	        state[ 1 ]  +=  b;
	        state[ 2 ]  +=  c;
	        state[ 3 ]  +=  d;
	    }
	
	     private   final   void  update( byte  input[],  int  len)  {
	         int  index  =  (( int ) (count  >>   3 ))  &   0x3f ;
	        count  +=  (len  <<</span>  3 );
	         int  partLen  =   64   -  index;
	         int  i  =   0 ;
	         if  (len  >=  partLen)  {
	            System.arraycopy(input,  0 , buffer, index, partLen);
	            transform(buffer,  0 );
	             for  (i  =  partLen; i  +   63    len; i  +=   64 )
	                transform(input, i);
	            index  =   0 ;
	        }   else   {
	            i  =   0 ;
	        }
	        System.arraycopy(input, i, buffer, index, len  -  i);
	    }
	
	     private   byte [] end()  {
	         byte  bits[]  =   new   byte [ 8 ];
	         for  ( int  i  =   0 ; i    8 ; i ++ )
	            bits[i]  =  ( byte ) ((count  >>>  (i  *   8 ))  &   0xff );
	         int  index  =  (( int ) (count  >>   3 ))  &   0x3f ;
	         int  padlen  =  (index    56 )  ?  ( 56   -  index) : ( 120   -  index);
	        update(padding, padlen);
	        update(bits,  8 );
	         return  encode(state,  16 );
	    }
	
	     //  Encode the content.state array into 16 bytes array
	     private   byte [] encode( int  input[],  int  len)  {
	         byte  output[]  =   new   byte [len];
	         int  i  =   0 ;
	         int  j  =   0 ;
	         for  (; j   len; i ++ , j  +=   4 )  {
	            output[j]  =  ( byte ) ((input[i])  &   0xff );
	            output[j  +   1 ]  =  ( byte ) ((input[i]  >>   8 )  &   0xff );
	            output[j  +   2 ]  =  ( byte ) ((input[i]  >>   16 )  &   0xff );
	            output[j  +   3 ]  =  ( byte ) ((input[i]  >>   24 )  &   0xff );
	        }
	         return  output;
	    }
	
	     /**
	     * Get the digest for our input stream. This method constructs the input
	     * stream digest, and return it, as a a String, following the MD5 (rfc1321)
	     * algorithm,
	     * 
	     *  @return  An instance of String, giving the message digest.
	     *  @exception  IOException
	     *                Thrown if the digestifier was unable to read the input
	     *                stream.
	      */
	
	     public   byte [] getDigest()  throws  IOException  {
	         byte  buffer[]  =   new   byte [BUFFER_SIZE];
	         int  got  =   - 1 ;
	
	         if  (digest  !=   null )
	             return  digest;
	         while  ((got  =  in.read(buffer))  >   0 )
	            update(buffer, got);
	         this .digest  =  end();
	         return  digest;
	    }
	
	     /**
	     * Get the digest, for this string digestifier. This method doesn't throw
	     * any IOException, since it knows that the underlying stream ws built from
	     * a String.
	      */
	
	     public   byte [] processString()  {
	         if  ( ! stringp)
	             throw   new  RuntimeException( this .getClass().getName()
	                     +   " [processString] "   +   "  not a string. " );
	         try   {
	             return  getDigest();
	        }   catch  (IOException ex)  {
	        }
	         throw   new  RuntimeException( this .getClass().getName()
	                 +   " [processString] "   +   " : implementation error. " );
	    }
	
	     /**
	     * Get the digest, as a proper string.
	      */
	
	     public  String getStringDigest()  {
	         if  (digest  ==   null )
	             throw   new  RuntimeException( this .getClass().getName()
	                     +   " [getStringDigest] "   +   " : called before processing. " );
	         return  stringify(digest);
	    }
	
	     /**
	     * Construct a digestifier for the given string.
	     * 
	     *  @param  input
	     *            The string to be digestified.
	     *  @param  encoding
	     *            the encoding name used (such as UTF8)
	      */
	
	     public  MD5(String input, String enc)  {
	         byte  bytes[]  =   null ;
	         try   {
	            bytes  =  input.getBytes(enc);
	        }   catch  (UnsupportedEncodingException e)  {
	             throw   new  RuntimeException( " no  "   +  enc  +   "  encoding!!! " );
	        }
	         this .stringp  =   true ;
	         this .in  =   new  ByteArrayInputStream(bytes);
	         this .state  =   new   int [ 4 ];
	         this .buffer  =   new   byte [ 64 ];
	         this .count  =   0 ;
	        state[ 0 ]  =   0x67452301 ;
	        state[ 1 ]  =   0xefcdab89 ;
	        state[ 2 ]  =   0x98badcfe ;
	        state[ 3 ]  =   0x10325476 ;
	    }
	
	     /**
	     * Construct a digestifier for the given string.
	     * 
	     *  @param  input
	     *            The string to be digestified.
	      */
	
	     public  MD5(String input)  {
	         this (input,  " UTF8 " );
	    }
	
	     /**
	     * Construct a digestifier for the given input stream.
	     * 
	     *  @param  in
	     *            The input stream to be digestified.
	      */
	
	     public  MD5(InputStream in)  {
	         this .stringp  =   false ;
	         this .in  =  in;
	         this .state  =   new   int [ 4 ];
	         this .buffer  =   new   byte [ 64 ];
	         this .count  =   0 ;
	        state[ 0 ]  =   0x67452301 ;
	        state[ 1 ]  =   0xefcdab89 ;
	        state[ 2 ]  =   0x98badcfe ;
	        state[ 3 ]  =   0x10325476 ;
	    }
	
	     public   static   void  main(String args[])  throws  IOException  {
	         if  (args.length  !=   1 )  {
	            System.out.println( " Md5  " );
	            System.exit( 1 );
	        }
	        MD5 md5  =   new  MD5( new  FileInputStream( new  File(args[ 0 ])));
	         byte  b[]  =  md5.getDigest();
	        System.out.println(stringify(b));
	    }
	
	} 