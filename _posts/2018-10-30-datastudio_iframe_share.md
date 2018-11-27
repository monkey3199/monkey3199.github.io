---
title: "[Google] 구글 데이터 스튜디오로 만든 보고서를 iframe으로 삽입하기"
categories: develop etc

---

<h1 id="google-datastudio-리포트-임베디드하기">Google Datastudio 리포트 임베디드하기</h1>
<h2 id="데이터-스튜디오">데이터 스튜디오</h2>
<p>구글 데이터 스튜디오는 데이터 시각화 도구로 대시보드 및 보고서를 작성할 수 있도록 도와준다. 자세한 내용은 <a href="https://support.google.com/datastudio/answer/6283323?hl=ko">데이터 스튜디오</a>를 참고하길 바란다.</p>
<h2 id="html-iframe으로-임베디드-하기">HTML iframe으로 임베디드 하기</h2>
<p>이 글에서는 미리 보고서를 완성했다는 가정하에 내용을 진행한다.</p>
<h3 id="공유설정-하기">공유설정 하기</h3>
<p>보고서를 완성했다면, 보고서를 다른 웹페이지에서 볼 수 있도록 공유해줘야 한다. 공유하는 방법은 데이터 스튜디어 상단에 다른 사용자와 공유 아이콘을 선택한 뒤 적절한 공유 옵션을 선택해서 공유하는 것이다. 다음은 가능한 공유 옵션이다.</p>
<ul>
<li>공유 가능한 링크 가져오기(전체 공유)</li>
<li>특정 유저나 구글 그룹을 선택해서 공유하기</li>
<li>G Suite 유저에게 공유하기</li>
</ul>
<h3 id="보고서-삽입하기">보고서 삽입하기</h3>
<p>파일 &gt; 보고서 삽입을 선택하거나 상단에 &lt;&gt; 버튼을 누르면 보고서 삽입을 할 수 있게 자동으로 iframe 태그가 생성된다. 해당 설정을 이용해서 만들어진 코드를 복사해서 자신의 HTML 소스에 포함하면 보고서를 쉽게 삽입할 수 있다.</p>
<h2 id="참고">참고</h2>
<p><a href="https://support.google.com/datastudio/answer/7450249?hl=en">Google datastudio tutorial</a></p>

