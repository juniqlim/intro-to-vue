# Vue.js 소개

## 1. Vue.js?
Front-end framework  
  
[Vue.js 공식가이드](https://kr.vuejs.org/v2/guide/)
>사용자 인터페이스를 만들기 위한 진보적인 프레임워크  

>다른 단일형 프레임워크와 달리 Vue는 점진적으로 채택할 수 있도록 설계하였습니다.  
>핵심 라이브러리는 뷰 레이어만 초점을 맞추어 다른 라이브러리나 기존 프로젝트와의 통합이 매우 쉽습니다.

## 2. Hello world 찍기
[https://jsfiddle.net/juniq/69dczp4y](https://jsfiddle.net/juniq/69dczp4y)
```
<div id="app">
    {{ message }}
</div>

<script src="https://unpkg.com/vue"></script>
<script>
    var app = new Vue({
        el: '#app',  // html 코드와 매칭되는 아이디
        data: { // 데이터
            message: 'Hello world!'
        }
    });
</script>
```
new Vue 로 vue 인스턴스를 만들었다.
el 로 html 코드와 연결되고, data에 있는 데이터가 화면에 보여진다.
연결된 이후 반응형이 된다.

## 3. 데이터 바인딩
[https://jsfiddle.net/juniq/xrpvy6jh](https://jsfiddle.net/juniq/xrpvy6jh)
```
<div id="app">
    {{ message }}
    <br>
    <input type="text" v-model="message"> <!-- app.message 에 연결(바인딩) -->
</div>

<script src="https://unpkg.com/vue"></script>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            message: 'hello!'
        }
    });
</script>
```
양방향 데이터 바인딩
v-model 디렉티브로 app.message 속성과 연결, 최신상태를 유지(반응형)하게 된다.
같은 속성에 연결된 template 요소들이 같이 변경된다. 

## 4. 이벤트 핸들링
[https://jsfiddle.net/juniq/ovwkp182](https://jsfiddle.net/juniq/ovwkp182)
```
<div id="app">
    {{ message }}
    <br>
    <input type="text" v-model="message">
    <button v-on:click="deleteMessage">삭제</button> <!-- 클릭 이벤트를 deleteMessage method와 연결 -->
</div>

<script src="https://unpkg.com/vue"></script>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            message: 'hello!'
        },
        methods: { // 메소드
            deleteMessage: function () {
                this.message = '';
            }
        }
    });
</script>
```
버튼에 v-on 디렉티브로 클릭리스너를 설정하고 deleteMessage 메소드가 실행하도록 함.
deleteMessage 는 app.message 속성을 빈값으로 만들어 app.message에 바인딩 되어있는 template 요소들도 반영된다. 

## 5.간단한 페이지 만들기(api server call)
jquery+handlebars.js 
```
<div>
    <!-- 목록 시작 -->
    <table id="list">
    </table>
    <!-- 목록 끝 -->
    <br>
    <!-- 상세 시작 -->
    <div id="detail">
    </div>
    <!-- 상세 끝 -->
</div>

<!-- 목록 item 시작 -->
<script id="item-template" type="text/x-handlebars-template">
    {{#each list}}
        <tr>
            <td>{{id}}</td>
            <td>{{name.family}}</td>
            <td>{{name.given}}</td>
            <td>{{mobilePhoneNumber}}</td>
            <td><button id="btnDetail" value="{{id}}">상세</button></td>
        </tr>
    {{/each}}
</script>
<!-- 목록 item 끝 -->

<!-- 상세 inner 시작 -->
<script id="detail-template" type="text/x-handlebars-template">
    <table>
        <tr>
            <td><input type="text" id="familyName" value="{{name.family}}"></td>
        </tr>
        <tr>
            <td><input type="text" id="givenName" value="{{name.given}}"></td>
        </tr>
        <tr>
            <td><input type="text" id="mobilePhoneNumber" value="{{mobilePhoneNumber}}"></td>
        </tr>
    </table>
    <br>
    <button id="btnUpdate" value="{{id}}">수정</button>
</script>
<!-- 상세 inner 끝 -->

<style>
    table {
        border: 1px solid #444444;
        border-collapse: collapse;
    }
    th, td {
        border: 1px solid #444444;
    }
</style>

<script src="https://unpkg.com/jquery"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.1.2/handlebars.min.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
    (function($, win, doc) {

        function getList() {
            axios.get('/api/v1/patients').then(function (res) {
                $('#list').empty().append(Handlebars.compile($('#item-template').html())(res.data.data.patients));
            }).catch();
        }
        function getItem(id) {
            axios.get('/api/v1/patients/'+id).then(function(res) {
                $('#detail').empty().append(Handlebars.compile($('#detail-template').html())(res.data.data.patient));
            }).catch();
        }
        function update(id) {
            var body = {
                fields: ["name","mobilePhoneNumber"],
                name: {
                    family: $('#familyName').val(),
                    given: $('#givenName').val()
                },
                mobilePhoneNumber: $('#mobilePhoneNumber').val()
            }
            axios.patch('/api/v1/patients/'+id, body).then(function (res) {
                getList();
                $('#detail').empty();
            }).catch();
        }

        $(doc).on('click', '#btnDetail', function(e) {
            getItem($(e.target).val());
        }).on('click', '#btnUpdate', function(e) {
            update($(e.target).val());
        }).on('keyup', '#familyName, #givenName, #mobilePhoneNumber', function(e) {
            if (e.keyCode == 13) {
                update($('#btnUpdate').val());
            }
        });

        win.onload = function () {
            getList();
        };
    })(jQuery, window, document);
</script>
```
  
Vue.js
```
<div id="app">
    <!-- 목록 시작 -->
    <table>
        <tr v-for="item in list">
            <td>{{item.id}}</td>
            <td>{{item.name.family}}</td>
            <td>{{item.name.given}}</td>
            <td>{{item.mobilePhoneNumber}}</td>
            <td><button @click="getItem(item.id)">상세</button></td>
        </tr>
    </table>
    <!-- 목록 끝 -->
    <br>
    <!-- 상세 시작 -->
    <template v-if="Object.keys(item).length">
        <table>
            <tr>
                <td><input type="text" v-model="item.name.family" @change="addField('name')" @keyup.enter="update(item.id)"></td>
            </tr>
            <tr>
                <td><input type="text" v-model="item.name.given" @change="addField('name')" @keyup.enter="update(item.id)"></td>
            </tr>
            <tr>
                <td><input type="text" v-model="item.mobilePhoneNumber" @change="addField('mobilePhoneNumber')" @keyup.enter="update(item.id)"></td>
            </tr>
        </table>
        <br>
        <button @click="update(item.id)">수정</button>
    </template>
    <!-- 상세 끝 -->
</div>

<style>
    table {
        border: 1px solid #444444;
        border-collapse: collapse;
    }
    th, td {
        border: 1px solid #444444;
    }
</style>

<script src="https://unpkg.com/vue"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
    var vm = new Vue({
        el: '#app',
        data: {
            list: [],
            item: {}
        },
        methods: {
            getList: function () {
                var self = this;
                axios.get('/api/v1/patients').then(function (res) {
                    self.list = res.data.data.patients.list;
                }).catch();
            },
            getItem: function (id) {
                axios.get('/api/v1/patients/'+id).then((res) => { // es6 화살표 함수
                    this.item = res.data.data.patient;
                    this.item.fields = [];
                }).catch();
            },
            update (id) { // es6 객체 리터럴 프로퍼티 축약
                var self = this;
                axios.patch('/api/v1/patients/'+id, self.item).then(function (res) {
                    self.item = {};
                    self.getList();
                }).catch();
            },
            addField: function (field) {
                if (!this.item.fields.includes(field)) {
                    this.item.fields.push(field);
                }
            }
        },
        mounted: function () {
            this.$nextTick(function () { // 모든 화면이 렌더링된 후 실행합니다.
                this.getList();
            })
        }
    });
</script>
```
