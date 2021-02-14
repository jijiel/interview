# kakao_login_test

카카오 로그인 테스트

- javascript로 구현

  ```html
  <!doctype html>
  <html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Insert title here</title>
  </head>
  <body>
  	<a href="javascript:kakaoLogin();"><img src="https://www.gb.go.kr/Main/Images/ko/member/certi_kakao_login.png" style="height:60px;width:auto;"/></a>
  	<script src="https://developers.kakao.com/sdk/js/kakao.js"></script>
  	<script>
  	//32c7fa134f7f0ab52d95661081f3789c
  	window.Kakao.init("32c7fa134f7f0ab52d95661081f3789c");
  	
  	function kakaoLogin(){
  		window.Kakao.Auth.login({
  			scope: 'profile, account_email, gender',
  			success: function(authObj) {
  				console.log(authObj);
  				window.Kakao.API.request({
  					url:'/v2/user/me',
  					success: res => {
  						const kakao_account = res.kakao_account;
  						console.log(kakao_account);
  					}
  				});
  			}
  		});
  	}
  	</script>
  </body>
  </html>
  ```

- 코드 구현 이전에 kakao developers에서 테스트 로그인 할 항목들과 로그인 후 리다이렉트 할 주소를 먼저 설정해야한다.
- 구현 이미지

![](https://postfiles.pstatic.net/MjAyMTAyMTRfMTA5/MDAxNjEzMjk5NTA2MjU2.qfk3KTkhktG1xeXHLueq0VaCs7G0zSXS-9Rtp3tUoWcg.rzesoWFLlQ93velbYKTU9qOeVNGzQs3V3KuPx7j8F5sg.PNG.o_oax/image.png?type=w966)

![](https://postfiles.pstatic.net/MjAyMTAyMTRfMjU1/MDAxNjEzMjk5NTIyMTE4.mQnqbXQ2JdXx5p3veIGuMOM6ziByEpCSOcLeFO8T1kMg.-MhF-D1DvmTF8aUACLF50J0PAuYmljIAZbrVJa9LOU0g.PNG.o_oax/image.png?type=w966)

![](https://postfiles.pstatic.net/MjAyMTAyMTRfMTE2/MDAxNjEzMjk5NTQzMzU2.CL0e3V00b4L5y4W0zLy-8P2AlmsJDfHGYvBfH7zYyZYg.bTtcBE3V2r0O0NXFcd5-r42dn10qv1dUF7d9Qy2x5h4g.PNG.o_oax/image.png?type=w966)

- 로그인 후 동의하고 계속하기 하면 아래에 동의 항목들 값을 받아 온것을 확인 할 수 있다.

![](https://postfiles.pstatic.net/MjAyMTAyMTRfMjk4/MDAxNjEzMjk5NTk4MDQy.Y61iVKkhWFr7Gh1VaOG-7r0ORo5ReZVH7aon_KH24M4g.aV-3OJ7MGxX2C_oADPwGH-eO4tuiS8ZrQPDBCc_ZVxwg.PNG.o_oax/image.png?type=w966)

- 로그인 성공 메세지

![](https://postfiles.pstatic.net/MjAyMTAyMTRfMTky/MDAxNjEzMjk5Njc4MjIx.RAVjl7X2IPBDhG0CSc_qgCaCvjJMBGI6NuYqlUeKelkg.5A_RphR--b_5PK8IvfBM6Iy4rHnJRoZHbkRJIl-1U1wg.PNG.o_oax/image.png?type=w966)