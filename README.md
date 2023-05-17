<h1><img src="https://em-content.zobj.net/thumbs/160/microsoft/319/star_2b50.png" height="30px"/>nodejs based ejs template engine setting for window</h1>

![NodeJS](https://img.shields.io/badge/node.js-6DA55F?style=for-the-badge&logo=node.js&logoColor=white)
![HTML5](https://img.shields.io/badge/html5-%23E34F26.svg?style=for-the-badge&logo=html5&logoColor=white)
![ejs](https://img.shields.io/badge/ejs-black?style=for-the-badge&logo=ejs&logoColor=white)

<h3> Now, explain about ejs engine template engine </h3>

* nodejs기반 express + ejs템플릿 엔진 세팅
1. npm init -y
2. npm install
3. npm i nodemon
4. npm i express
5. npm i ejs
6. package.json file setting : script => "start": "nodemon app.js"
6. create app.js
7. create folder 'public'
8. public -> index.ejs
9. app.js setting : 아래의 코드를 app.js파일에 작성 후 활용하면 된다.
---------------------------------------
    const express = require('express');
    const app = express();

    app.set('view engine', "ejs");
    app.set('views', "./public");

    app.listen(7500, (req, res) => {
        console.log('express server open : ', 7500);
    });

    app.get('/', (req, res) => {
        res.render('index');
    })
---------------------------------------
10. 코드 분할 : app.js로 서버를 가동하고, 페이지 컨트롤러와 라우터 파일을 따로 생성하여 관리하면 가독성을 챙길 수 있다.
- app.js
---------------------------------------
    const express = require('express');
    const app = express();
    const router = require('./public/js/router');   // router파일 경로 등록

    app.set('view engine', "ejs");
    app.set("views", "./public/views");

    app.listen(7500, (req, res) => {
        console.log('express server open : ', 7500);
    });

    app.use('/', router);   // app.js 실행 후 웹 페이지 해당 경로 연결 시 router연결

    module.exports = app;
---------------------------------------
- router.js
---------------------------------------
    // 라우터에서는 클라이언트에서 요청하는 경로를 정의하고,
    // 해당 경로에서 요청이 오면 페이지컨트롤러에서 정의한 메서드를 호출하여 실행한다.
    const express = require('express');
    const router = express.Router();

    const ctrl = require('./pageCTRL');

    router.get("/", ctrl.output.index);
    router.get("/main", ctrl.output.main);

    module.exports = router;
---------------------------------------
- pageCTRL.js
---------------------------------------
    // 페이지 컨트롤러에서는 웹에서 사용할 ejs파일과 메서드를 정의
    const output = {
        index: (req, res) => {
            res.render('index');    // 파일 경로 (app.js에서 정의 해줬기 때문에 ".ejs"와 같은 확장자명은 생략 가능)
        },
        main: (req, res) => {
            res.render('main');    // 파일 경로 (app.js에서 정의 해줬기 때문에 ".ejs"와 같은 확장자명은 생략 가능)
        }
    }

    module.exports = {
        output
    };
---------------------------------------
11. ejs파일에서 사용할 수 있는 javascript 코드(편의 기능)
- 기본적으로 "<% %>"를 사용하여 기능을 적용시킨다.
- ejs파일에서 다른 ejs파일 호출
---------------------------------------
    // 아래의 코드와 같이 include + 파일경로
    <%- include header.ejs %>
---------------------------------------
- 변수 활용과 테스트
---------------------------------------
    <% var test = 0; %>
    <% console.log(test) %>
---------------------------------------
- if문, for문, foreach문 사용
---------------------------------------
    // if문
    <% if (test == 0) { %>
        <p>test의 값은 0</p>
    <% } else { %>
        <p>test의 값은 0이 아님</p>
    <% } %>

    // for문
    <% var count = [0,1,2,3,4,5,6,7]; %>
    <% for(let i=0; i < count.length; i++) { %>
        <% test = i; %>
        <% console.log("for문 값: ", test) %>
    <% } %>

    // foreach문
    <% count.forEach(function(num) { %>
        <% console.log("forEach문 값: ", num) %>
    <% }) %>
---------------------------------------
12. 서버단(app.js나 router.js 등 서버측의 코드를 포함하는 파일)에서 사용되는 변수를 ejs에서 사용
- 현재 정리하는 부분에서 설명을 하면,
- 페이지 컨트롤러 파일(pageCTRL.js)에서 사용하는 메서드 안에 ejs파엘에 랜더링해주는 코드가 있는데(ex. res.render('index');)
- 랜더링해주는 코드에 문자열을 포함해서 ejs 파일에 던지면 된다.
---------------------------------------
    // 기본 코드 : res.render('index');
    // 변형 코드
    let a = [0, 1, 2, 3];   // 배열도 가능
    let b = 1;
    let c = 2;
    var json = {
        a: a,
        b: b,
        c: c,
    };
    res.render('index', json);
    // 위 코드처럼 보내고 ejs파일에서 확인
    <% console.log(a, b, c) %>
    // 결과값 : [0, 1, 2, 3] 1 2
---------------------------------------

* 결론
- ejs는 서버측에서 HTML을 동적으로 생성하기 위한 템플릿 엔진이다.
- 잘 파악하고 사용하면 정말 편하고 쉽게 사용할 수 있는 것 같다. 즉, 작은 프로젝트를 수행하기에는 좋을 것 같다.
- 직접 프로젝트에 적용해서 작업을 해 봤는데, 작업량이 많아질수록 코드 구조가 복잡해지고, 추후에 유지보수할 때 어려움이 많이 따를 것이라 생각이 되어졌다.
- 만약, 큰 프로젝트에서 사용하려 한다면 프로젝트를 시작하기 전에 꼭! 구조를 체계적으로 잡고 가는 것이 중요할 것 같다.

