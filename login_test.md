# login_test

### 네이버 로그인 테스트

#### controller

```java
package com.test.naverlogin;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.URL;

import javax.net.ssl.HttpsURLConnection;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;


/**
 * Handles requests for the application home page.
 */
@Controller
public class HomeController {
	
	@RequestMapping("/naverlogin")
	public String isComplete(HttpSession session) {

		return "naverlogin";
	}

	
	@RequestMapping("/callback")
	public String naverLogin(HttpServletRequest request) throws Exception {

		return "callback";
	}
	
	@RequestMapping("/personalInfo")
	public void personalInfo(HttpServletRequest request)throws Exception {
		String token = "AAAANQuAJ1yZvHflq17LUIDrBvEN6kPYYP9rlPhsLXt9dmNk3iO-pLqOwgxk6rzcl9r8Eg1_5iEZRwAjtbbdKJAdUHQ";
		String header = "Bearer" + token;
		try {
			String apiURL = "https://openapi.naver.com/v1/nid/me";
			URL url = new URL(apiURL);
			HttpsURLConnection con = (HttpsURLConnection)url.openConnection();
			con.setRequestMethod("GET");
			con.setRequestProperty("Authorization", header);
			int responseCode = con.getResponseCode();
			BufferedReader br;
			if(responseCode ==200) {
				br = new BufferedReader(new InputStreamReader(con.getInputStream()));
			}else {
				br = new BufferedReader(new InputStreamReader(con.getErrorStream()));
			}
			String inputLine;
			StringBuffer response = new StringBuffer();
			while ((inputLine = br.readLine())!=null) {
				response.append(inputLine);
			}
			br.close();
			System.out.println(response.toString());
		} catch (Exception e) {
			System.out.println(e);
			// TODO: handle exception
		}
		
		
	}
	
}
```

#### profile

```java
package profile;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.HashMap;
import java.util.Map;

public class ApiExamMemberProfile {
	

	   public static void main(String[] args) {
	        String token = "AAAANQuAJ1yZvHflq17LUIDrBvEN6kPYYP9rlPhsLXt9dmNk3iO-pLqOwgxk6rzcl9r8Eg1_5iEZRwAjtbbdKJAdUHQ"; // 네이버 로그인 접근 토큰;
	        String header = "Bearer " + token; // Bearer 다음에 공백 추가

	        String apiURL = "https://openapi.naver.com/v1/nid/me";

	        Map<String, String> requestHeaders = new HashMap<String, String>();
	        requestHeaders.put("Authorization", header);
	        String responseBody = get(apiURL,requestHeaders);

	        System.out.println(responseBody);

}
	   
	   private static String get(String apiUrl, Map<String, String> requestHeaders){
	        HttpURLConnection con = connect(apiUrl);
	        try {
	            con.setRequestMethod("GET");
	            for(Map.Entry<String, String> header :requestHeaders.entrySet()) {
	                con.setRequestProperty(header.getKey(), header.getValue());
	            }

	            int responseCode = con.getResponseCode();
	            if (responseCode == HttpURLConnection.HTTP_OK) { // 정상 호출
	                return readBody(con.getInputStream());
	            } else { // 에러 발생
	                return readBody(con.getErrorStream());
	            }
	        } catch (IOException e) {
	            throw new RuntimeException("API 요청과 응답 실패", e);
	        } finally {
	            con.disconnect();
	        }
	    }

	    private static HttpURLConnection connect(String apiUrl){
	        try {
	            URL url = new URL(apiUrl);
	            return (HttpURLConnection)url.openConnection();
	        } catch (MalformedURLException e) {
	            throw new RuntimeException("API URL이 잘못되었습니다. : " + apiUrl, e);
	        } catch (IOException e) {
	            throw new RuntimeException("연결이 실패했습니다. : " + apiUrl, e);
	        }
	    }

	    private static String readBody(InputStream body){
	        InputStreamReader streamReader = new InputStreamReader(body);

	        try (BufferedReader lineReader = new BufferedReader(streamReader)) {
	            StringBuilder responseBody = new StringBuilder();

	            String line;
	            while ((line = lineReader.readLine()) != null) {
	                responseBody.append(line);
	            }

	            return responseBody.toString();
	        } catch (IOException e) {
	            throw new RuntimeException("API 응답을 읽는데 실패했습니다.", e);
	        }
	    }
	}
```

#### login 페이지

```jsp
<%@ page import="java.net.URLEncoder" %>
<%@ page import="java.security.SecureRandom" %>
<%@ page import="java.math.BigInteger" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>네이버로그인</title>
  </head>
  <body>
  <%
    String clientId = "yRg34kdZVGWIALdMH3TQ";//애플리케이션 클라이언트 아이디값";
    String redirectURI = URLEncoder.encode("http://localhost:8282/naverlogin/callback", "UTF-8");
    SecureRandom random = new SecureRandom();
    String state = new BigInteger(130, random).toString();
    String apiURL = "https://nid.naver.com/oauth2.0/authorize?response_type=code";
    apiURL += "&client_id=" + clientId;
    apiURL += "&redirect_uri=" + redirectURI;
    apiURL += "&state=" + state;
    session.setAttribute("state", state);
 %>
  <a href="<%=apiURL%>"><img height="50" src="http://static.nid.naver.com/oauth/small_g_in.PNG"/></a>
  </body>
</html>
```

#### callback 페이지

```jsp
<%@ page import="java.net.URLEncoder" %>
<%@ page import="java.net.URL" %>
<%@ page import="java.net.HttpURLConnection" %>
<%@ page import="java.io.BufferedReader" %>
<%@ page import="java.io.InputStreamReader" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>네이버로그인</title>
  </head>
  <body>
  <%
    String clientId = "yRg34kdZVGWIALdMH3TQ";//애플리케이션 클라이언트 아이디값";
    String clientSecret = "vKF_4RPN0r";//애플리케이션 클라이언트 시크릿값";
    String code = request.getParameter("code");
    String state = request.getParameter("state");
    String redirectURI = URLEncoder.encode("http://localhost:8282/naverlogin/callback", "UTF-8");
    String apiURL;
    apiURL = "https://nid.naver.com/oauth2.0/token?grant_type=authorization_code&";
    apiURL += "client_id=" + clientId;
    apiURL += "&client_secret=" + clientSecret;
    apiURL += "&redirect_uri=" + redirectURI;
    apiURL += "&code=" + code;
    apiURL += "&state=" + state;
    String access_token = "";
    String refresh_token = "";
    System.out.println("apiURL="+apiURL);
    try {
      URL url = new URL(apiURL);
      HttpURLConnection con = (HttpURLConnection)url.openConnection();
      con.setRequestMethod("GET");
      int responseCode = con.getResponseCode();
      BufferedReader br;
      System.out.print("responseCode="+responseCode);
      if(responseCode==200) { // 정상 호출
        br = new BufferedReader(new InputStreamReader(con.getInputStream()));
      } else {  // 에러 발생
        br = new BufferedReader(new InputStreamReader(con.getErrorStream()));
      }
      String inputLine;
      StringBuffer res = new StringBuffer();
      while ((inputLine = br.readLine()) != null) {
        res.append(inputLine);
      }
      br.close();
      if(responseCode==200) {
        out.println(res.toString());
      }
    } catch (Exception e) {
      System.out.println(e);
    }
  %>
  </body>
</html>
```

#### 구현이미지

![](https://postfiles.pstatic.net/MjAyMTAxMjRfMTE5/MDAxNjExNDk3NjMyNjgx.yMzdQTDqYj-9CSCkdjjNtLla05GgkmI1ujK3zqvZuEUg.Yw1KnBDAaAtreH0zH2xUc_9_m6M9O-q12EUpde1Td90g.PNG.o_oax/image.png?type=w966)

> 소셜 로그인 버튼 생성

![](https://postfiles.pstatic.net/MjAyMTAxMjRfMjYy/MDAxNjExNDk3NjYyNzE0.XJObdQsfaW3Fjqins2EzRVhzHHG-dnj4VIt7aF6k46kg.MVsfRYrqB-et7bFuyHY1cmE0NHwfsSiCwWFZmloiSVsg.PNG.o_oax/image.png?type=w966)

> callback 값을 받으면 토큰생성, 3600으로 되어있어 1시간마다 새로운 토큰 생성해야 접근가능

![](https://postfiles.pstatic.net/MjAyMTAxMjRfODEg/MDAxNjExNDk3NTk3MzUy.fjXYcg7GhmCe4I9WKvaPXhT237J7_-W540rq6whC6Dog.xZhwbp5HHUi9VsXoNNXkg7ATWHyPeNC1F3SL8kxBzvkg.PNG.o_oax/image.png?type=w966)

> 자바파일로 직접 프로필이 유효한지 확인 할 수 있다.

![](https://postfiles.pstatic.net/MjAyMTAxMjRfMjIw/MDAxNjExNDk3NjkyOTU4._ipOrJv8SNOLat5g0vfr8xZ0A5EzG_rINx-XvfN_158g.ahXOvWOGPNs_EZZlvRVb41g7JpxE1y9D2FjVnIYc_BIg.PNG.o_oax/image.png?type=w966)

> 직접 url로 들어가면 권한이 없다고 하며 프로필 유효한지 확인 불가능
