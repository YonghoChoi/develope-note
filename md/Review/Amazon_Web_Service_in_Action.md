최근에 사내 프로젝트를 진행하면서 각 서버와 데이터베이스에 대한 장애처리나 분산 처리와 같은 부분에서 고민을 많이 하고 있었습니다. 그래서 docker를 도입해보기도 하고, Jmeter를 통한 부하 테스트 및 데이터베이스 분산을 위한 샤딩 구조 설계까지 팀 내 서버 개발자 3명이서 많은 시도와 고민을 수개월째 이어나가고 있던 중이었습니다. 결국에는 적은 인원으로 너무 많은 부분을 관리해야 하는 부담과 위험성 때문에 클라우드 쪽으로 방향을 틀었고, Amazon Web Service(이하 AWS)를 도입하게 되었습니다. 

AWS의 서비스들을 하나씩 알아가면서 매일 감탄과 신세계를 경험했습니다. 먼저 데이터베이스는 RDS 서비스를 이용하면(그 중 Aurora) 가장 걱정 했던 샤딩을 내부적으로 알아서 해주고, 읽기 성능과 고가용성을 위해 레플리카를 구성하여 update와 delete를 담당하는 데이터베이스 서버와 select를 담당하는 데이터베이스 서버를 분리하여 읽기 성능을 극대화 시킬 수 있었습니다. 그러면서 이 전에 읽기 성능을 위해 중간에 redis를 두어서 캐싱을 하던 부분이 불필요하다고 판단하여 이를 제거함으로써 개발 속도도 빨라지고 복잡성도 줄어들게 되었습니다.

이 처럼 AWS의 서비스들을 잘 활용하면 진행 중인 프로젝트의 복잡성을 줄이고 더 안정적으로 서비스할 수 있습니다. 이를 위해 여러 AWS에 관한 책을 읽어보았지만 아마존 웹 서비스 인 액션은 개인적으로 최고의 책이었습니다. 이 책을 읽기 시작한 시점을 돌이켜보면 딱 저에게 잘 맞는 수준의 책이었다고 생각합니다. 

![]()

먼저, 이 책은 완전 초보용 책은 아니라고 생각합니다. 아마존 콘솔을 사용하여 설명을 이어가는 다른 책들과는 달리 이 책은 AWS CLI와 CloudFormation이라는 AWS의 서비스를 사용하여 설명을 하고 있습니다.(물론, AWS 콘솔으로 설명하는 부분도 있습니다.) 그러므로 command 환경에 조금은 익숙해져 있는 것이 좋고, Amazon CloudFormation에 대해서는 이해할 수 있을 정도로 숙지를 하고 있어야 합니다. CloudFormation에 대한 설명이 이 책에 포함되어 있긴 하지만 CloudFormation 코드를 보고 이해할 수 있는 정도의 수준이 되기에는 설명이 조금 부족하다고 생각합니다. 하지만 [AWS의 CloudFormation 사용 설명서](http://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/Welcome.html)에 아주 상세하게 나와있으므로 부족한 부분은 이를 참고하면 충분할 것입니다. 이에 더해 제가 이전에 [Docker for AWS CloudFormation을 분석한 포스팅](http://yongho1037.tistory.com/725)을 참고하시면 도움이 될 것이라 생각됩니다.

![]()

이 책이 좋았다고 생각했던 부분 중에 또 하나는 위에 언급했던 AWS CLI와 CloudFormation을 통해 자동화를 할 수 있다는 것이었습니다. 브라우져에서 AWS 콘솔을 사용하여 서비스를 구성하게 되면 다음번에 같은 구성을 하기 위해 절차를 모두 기억하거나 어딘가에 기록을 해두어야 하고, 사람의 손으로 진행하는 것이기 때문에 실수를 할 소지가 다분합니다. 하지만 AWS CLI와 CloudForamtion을 활용한다면 실수없이 같은 환경을 구성하는 것이 너무나 쉽게 가능해집니다. 코드를 활용한 인프라 구성에 대한 장점에 대해 더 알고싶으신 분들은 한빛미디어의 [코드로 인프라 관리하기](http://www.hanbit.co.kr/media/books/book_view.html?p_code=B6496581381)(AWS 관련 내용은 아님)를 참고하시면 좋을 것 같습니다.

AWS의 서비스에는 아래와 같이 수많은 서비스들이 존재합니다. 그 중 이 책에서 다루는 서비스들은 빨간 밑줄이 그어져 있는 서비스들입니다.

![]()

다루지 않는 서비스들이 압도적으로 많기는 하지만 책에서 다루는 서비스들이 주요 서비스들에 해당합니다. 이 주요 서비스들을 조금 더 편하게 사용하고 도움을 주는 서비스들도 많이 존재하고 있으므로 책의 내용을 잘 숙지하면 다른 서비스들을 도입하는데에 훨씬 더 수월할 것이라 생각됩니다. 

예를 들어 트래픽에 따른 부하 분산과 고가용성을 위해 EC2와 Auto Scaling Group, ELB를 사용하면 큰 노력없이 구성이 가능하지만 Amazon Elastic Beanstalk라는 서비스를 사용하면 이마저도 개발자가 신경쓸 필요 없이 클릭 몇 번, 명령어 몇 번 만으로 손쉽게 구성을 해주고 모니터링과 설정 관리 및 업데이트, 배포까지 해줍니다. 이 서비스를 본 순간 팀 전체가 충격에 휩싸이기도 했었습니다.

간단히 단일 호스트에 Wordpress 서버 구축부터 시작하여 오토 스케일링, 데이터베이스, 고가용성까지 성공적인 서비스 런칭을 위한 모든 과정이 담겨져 있으므로 아마존 공식 사용설명서와 더불어서 차분히 숙지해나가면 문제 없이 서비스를 런칭 할 수 있을 것이라 생각합니다. 