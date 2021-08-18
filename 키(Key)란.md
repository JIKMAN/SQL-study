> **키(Key)란?**

키는 데이터베이스에서 조건에 만족하는 튜플을 찾거나 순서대로 정렬할 때 다른 튜플들과 구별할 수 있는 유일한 기준이 되는 속성이다.



> **1. 후보키(Candidate Key)**

-릴레이션을 구성하는 속성들 중에서 튜플을 유일하게 식별할 수 있는 속성들의 부분집합을 의미한다.

-기본키로 사용할 수 있는 속성들을 말한다.

-하나의 릴레이션에서는 중복된 튜플이 있을 수 없으므로 모든 릴레이션에는 반드시 하나이상의 후보키가 존재한다.

-후보키는 릴레이션에 있는 모든 튜플에 대해서 유일성과 최소성을 만족시켜야 한다.



아래 예시에서는 학생번호와 주민번호가 후보키가 될 수 있다 하지만 이름은 될 수 없다 왜냐하면 학생번호와 주민번호는 유일하지만 이름은 동명이인이 있을 수 있기에 불가능하다.

<img src="https://postfiles.pstatic.net/MjAyMTA3MjdfMjIz/MDAxNjI3MzU1Mjc5Mzc2.0LTFCe_o8EriHJFttauQvRc5ri8PgD75ybPdpswnj9Ag.RyxfglXNTMjcM6ruAn9RDzLFMVKusd9OEW3oQNuSJ5gg.PNG.hanjm1025/image.png?type=w773" alt="img" style="zoom:67%;" />





> **2.기본키(Primary Key)**

-후보키 중에서 선택한 주키(Main Key)이다.

-한 릴레이션에서 특정 튜플을 유일하게 구별할 수 있는 속성이다.

-Null 값을 가질 수 없다.

-기본키로 정의된 속성에는 동일한 값이 중복되어 저장될 수 없다

<img src="https://postfiles.pstatic.net/MjAyMTA3MjdfMjA4/MDAxNjI3MzU1MTg0MDc0.WcrDXq_-rhA6vjiHCyZ7PFPgh3eDP6Ao6rezU5YnG1gg.G18ZM9GkNcqOctt5RqcASQ2zdEghbSVA3FPcskPVzKgg.PNG.hanjm1025/image.png?type=w773" alt="img" style="zoom:67%;" />



> **3.대체키(Alternate Key)**

-후보키가 둘 이상일 때 기본키를 제외한 나머지 후보키들을 말한다.

-보조키라고도 한다.



학생번호가 기본키가 되어서 주민번호가 대체키가 되었지만 그 반대도 가능하다.

<img src="https://postfiles.pstatic.net/MjAyMTA3MjdfMjg4/MDAxNjI3MzU1MzgzMzgw.4KfmJRMH0TZnmvIdikJjStkat_kfU-CTMOih4pVvY3Ig.lKA-zOUJ_klPLlq6nJ4hq0ndIUw7sUxCv4Flx-qIWvgg.PNG.hanjm1025/image.png?type=w773" alt="img" style="zoom:67%;" />





> **4.슈퍼키(Super Key)**

-슈퍼키는 한 릴레이션 내에 있는 속성들의 집합으로 구성된 키로서 릴레이션을 구성하는 모든 튜플들 중 슈퍼키로 구성된 속성의 집합과 동일한 값을 나타나지 않는다.



아래 표를 보자

주민번호+이름은 가능하다. 하지만 이름만 따로 할 수는 없다.

학생번호+이름은 가능하다. 하지만 이름을 따로 할 수는 없다.

이름+나이는 불가능하다. 이름과 나이가 같은 사람이 또 있을 수 있기 때문이다.

<img src="https://postfiles.pstatic.net/MjAyMTA3MjdfNDQg/MDAxNjI3MzU1NjEyNDcz.RJa8kRYAg39EKUkCaBk4nUHnyvbELJ22-cRQkJWigXcg.CwAqCylrUwcIvPK4-wWZEk-bXNDjsGpjTEsyB2qtMAog.PNG.hanjm1025/image.png?type=w773" alt="img" style="zoom:67%;" />

> **5.외래키(Foreign Key)**

-관계를 맺고 있는 릴레이션 R1,R2에서 R1이 참조 하고 있는 R2의 기본키와 같은 R1의 릴레이션 속성

-외래키는 참조되는 릴레이션의 기본키와 대응되어 릴레이션 간에 참조 관계를 표현하는데 중요한 도구로 사용된다.

-외래키로 지정되면 참조테이블의 기본키에 없는 값은 입력할 수 없다 (참조 무결성)

<img src="https://postfiles.pstatic.net/MjAyMTA3MjdfMTAx/MDAxNjI3MzU2MDcxMjg0.Q4Ys9dA9oW2DM_-xyhG08FeJaTRR7RmhkIRMPrBii9cg.4HW8jrcpRNCaZLpHXLtc30TFyqLwumUdgzHVP7R-4G0g.PNG.hanjm1025/image.png?type=w773" alt="img" style="zoom:67%;" />