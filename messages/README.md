# messages

## 목차

- **Section 03. Nest CLI로 프로젝트 생성하기 완료**
  - [10. 앱 셋업](#10앱-셋업)
  - [11. Nest CLI로 파일 생성하기](#11-nest-cli로-파일-생성하기)
  - [12. 파일 생성에 관한 더 자세한 설명](#12-파일-생성에-관한-더-자세한-설명)
  - [13. 라우팅 논리 넣기](#13-라우팅-논리-넣기)
  - [15. VSCode REST Client 익스텐션 (선택)](#15-vscode-rest-client-익스텐션-선택)

- **Section 04. 파이프로 요청 데이터 검증하기**
  - [16.데코레이터로 요청 데이터에 액세스하기](#16-데코레이터로-요청-데이터에-엑세스하기)
  - [17. 추가 필수 라이브러리 설치하기](#17-추가-필수-라이브러리-설치하기)
  - [18. ValidationPipe 사용하기](#18-validationpipe-사용하기)
  - [19. 검증 규칙 추가하기](#19-검증-규칙-추가하기)
  - [20. 검증 과정 심층 분석](#20검증-과정-심층-분석)
  - [21. 타입 정보가 Javascript에서도 보존되는 이유](#21-타입-정보가-javascript에서도-보존되는-이유)
- **Section 05. Nest 아키텍쳐: 서비스와 리포지토리**
  - [22. 서비스와 리포지토리](#22-서비스와-리포지토리)
  - [23. 리포지토리 구현하기](#23-리포지토리-구현하기)
  - [24. 스토리지 파일 읽고 쓰기](#24-스토리지-파일-읽고-쓰기)
  - [25. 서비스 구현하기](#25-서비스-구현하기)
  - [26. 컨트롤러 수동 테스트](#26-컨트롤러-수동-테스트)
  - [27. Nest에 내장된 예외 라이브러리로 오류 보고하기](#27-nest에-내장된-예외-라이브러리로-오류-보고하기)
  - [28. 제어 역전 자세히 알아보기](#28-제어-역전-자세히-알아보기)
  - [29. 의존성 주입 소개](#29-의존성-주입-소개)
  - [30. 의존성 주입을 이용하기 위한 리팩터링](#30-의존성-주입을-이용하기-위한-리팩터링)
  - [31. 의존성 주입에 관한 추가 참고사항](#31-의존성-주입에-관한-추가-참고사항)

---

## Section 03. Nest CLI로 프로젝트 생성하기 완료

### 10.앱 셋업

nestJs cli를 설치하고 프로젝트 생성
`npm i -g @nestjs/cli`
`nest new section-03`

GET 요청과 POST 요청에 대한 프로세스 설명

1. Pipe: 요청에 포함된 데이터 유효성 검사
2. Guard: 사용자가 인증되었는지 확인합니다.
3. Controller: 요청을 특정 함수로 라우팅하기
4. Service: 일부 비즈니스 로직 실행
5. Repository: 데이터베이스에 액세스

GET은 Controller, Service, Repository 과정을,
POST는 Pipe, Controller, Service, Repository
에 대한 처리를 알아보도록 한다.

### 11. Nest CLI로 파일 생성하기

nestjs cli로 생성한 프로젝트에서 기본 app으로 시작하는 모듈, 컨트롤러, 서비스를 삭제하고
nestjs generate 를 이용하여 messages 모듈을 생성한다.
`nest generate module messages`

### 12. 파일 생성에 관한 더 자세한 설명

이번에는 messages controller를 만들어본다.
`nest generate controller messages/messages --flat`
_messages 폴더에 controller를 생성하는데 클래스 이름을 messages로 하겠다는 의미이다.
`--flat`옵션은 messages에 하위 폴더로 controllers를 추가로 생성하지 않는 옵션이다._

`messages.controller.spec.ts`, `messages.controller.ts`, `messages.module.ts` 파일이 생성되었다.

### 13. 라우팅 논리 넣기

messages.controller에 `Get, Post, Get('/:id)` 데코레이터로 라우팅 핸들러 메서드를 만들었다.

### 15. VSCode REST Client 익스텐션 (선택)

`rest client` 익스텐션을 설치하고, request.http 파일을 생성하여 요청 메서드를 작성하고 실행해보는 시간이었다.
_POST 요청을 보낼 때 Body는 Headers 밑에 한 줄을 띄어서 작성해야한다._

```http
### List all messages
GET http://localhost:3000/messages

### Create a new message
POST http://localhost:3000/messages
Content-Type: application/json

{
  "content": "hi there"
}

### Get a particular message
GET http://localhost:3000/messages/20250609
```

---

## Section 04. 파이프로 요청 데이터 검증하기

### 16. 데코레이터로 요청 데이터에 엑세스하기

`@Param('id')`, `@Query()`, `@Headers()`, `@Body()`

데코레이터는 클래스 데코레이터와, 메서드 데코레이터로 나누어진다

### 17. 추가 필수 라이브러리 설치하기

`npm i class-validator class-transformer`

### 18. ValidationPipe 사용하기

특정 라우트에 대한 요청이 들어왔을 때, 컨트롤러(라우트 핸들러)에 바로 연결되는 것이 아닌 미들웨어로 검증 파이프를 거치도록 설정하였다.

```ts
// main.ts
...
import { ValidationPipe } from '@nestjs/common'; // 추가

async function bootstrap() {
  const app = await NestFactory.create(MessagesModule);
  app.useGlobalPipes(new ValidationPipe()); // 추가
  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

### 19. 검증 규칙 추가하기

 `CreateMessageDto` 클래스를 추가하였다.

 ```ts
 // create-message.dto.ts
import { IsString } from "class-validator";

export class CreateMessageDto {
  @IsString()
  content: string;
}
 ```

> DTO(Data Transfer Object) 데이터 전송 객체
> 프로세스 간에 데이터를 전달하는 객체이다. 프로세스 간 통신이 일반적으로 원격 인터페이스(예: 웹 서비스)로 재정렬하면서 이루어지게 되는데 여기에서 각 호출의 비용이 많다는 점을 동기로 하여 이용하게 된다.각 호출의 비용이 큰 것이 클라이언트와 서버 간 왕복 시간과 관련되기 때문에 호출의 수를 줄이기 위해 여러 호출에 의해 전송되는 데이터를 축적하면서 오직 하나의 호출만으로 서비스되는 객체인 DTO를 사용하는 것이다.

### 20.검증 과정 심층 분석

`class-transformer` 패키지는 JSON 형식 데이터 또는 자바스크립트 객체를 클래스 인스턴스로 만드는 기능을 제공하며, 반대로 바꾸는 것도 가능하다.
`class-validator` 패키지는 데코레이터 기반 및 비데코레이터 기반 유효성 검사 기능을 제공한다. 내부적으로 validator.js를 사용하여 유효성 검사를 수행합니다. 브라우저와 Node.js 플랫폼 모두에서 작동한다.

ValidationPipe에 대한 과정을 아래와 같이 구성한다.

1. `class-transformer`를 사용하여 DTO 클래스 인스턴스로 변환한다.
2. `class-validator`를 이용하여 인스턴스에 있는 다양한 속성들을 검증한다.
3. 검증 오류가 있는지 확인하고 오류가 있으면 즉시 응답을 반환하고, 그렇지 않다면 컨트롤러 안에 정의한 요청 핸들러에게 요청을 전달한다.

### 21. 타입 정보가 JavaScript에서도 보존되는 이유

일반적으로 타입스크립트는 자바스크립트 코드로 변환될 때 타입은 보존되지 않는다.

`tsconfig.json`에서 설정한 옵션들 로 인해 아주 적은 양의 타입 어노테이션과 정보가 자바스크립트로 변환 된다.

```json
{
  "emitDecoratorMetadata": true,
  "experimentalDecorators": true,
}
```

변환 결과를 아래 코드로 확인하자.

```ts
// messages.controller.ts
@Post()
createMessage(@Body() body: CreateMessageDto) {
  console.log(body);
}
```

위 소스 코드가 아래 자바스크립트 소스 코드로 변환되었다.
데코레이터와, DTO 클래스 타입을 유지하고 있다.

```js
__decorate([
// dist/messages/messages.controller.js
    (0, common_1.Post)(),
    __param(0, (0, common_1.Body)()),
    __metadata("design:type", Function),
    __metadata("design:paramtypes", [create_message_dto_1.CreateMessageDto]),
    __metadata("design:returntype", void 0)
], MessagesController.prototype, "createMessage", null);
```

---

## Section 05. Nest 아키텍쳐: 서비스와 리포지토리

### 22. 서비스와 리포지토리

컨트롤러 -> 서비스 -> 리포지토리

서비스: 비즈니스 로직
리포지토리: 스토리지 관련 로직

흔히 서비스에 있는 메서드와 리포지토리에 있는 메서드와 똑같이 구성된다.
그럼에도 서비스와 리포지토리로 역할을 분리해야 하는지 앞으로 실습을 통해 배울 수 있다고 한다.

역할을 분리하면 저장소 교체나 변경에 대응하기 좋을 것 같다.

### 23. 리포지토리 구현하기

`messages.repository.ts`, `messages.service.ts`, `messages.json` 파일을 생성하였다.

`messages.repository.ts` 파일에서 클래스를 만들고 `findOne`, `findAll`, `create` 메서드 틀만 생성하였다.

`messages.json` 파일을 데이터를 저장하는 공간으로 사용할 수 있도록 생성한다.

### 24. 스토리지 파일 읽고 쓰기

데이터 구성을 파일 시스템을 통해 `messages.json` 파일을 읽고 객체로 변환하여 사용하였다.
`create` 메서드에서는 id에 해당하는 속성에 객체 데이터를 추가하여, `messages.json` 파일로 쓰기 작업을 추가하였다.

```ts
  async findOne (id: string) {
    const contents = await readFile('messages.json', 'utf-8');
    const messages = JSON.parse(contents);

    return messages[id];
  }

  async findAll() {
    const contents = await readFile('messages.json', 'utf-8');
    const messages = JSON.parse(contents);

    return messages;
  }

  async create(content: string) {
    const contents = await readFile('messages.json', 'utf-8');
    const messages = JSON.parse(contents);

    const id = Math.floor(Math.random() * 999);
    messages[id] = { id, content };

    await writeFile('messages.json', JSON.stringify(messages));
  }
```

### 25. 서비스 구현하기

`MessagesService` 클래스 생성 하여 리포지토리 클래스를 생성자에서 직접적으로 의존성을 부여하고 똑같은 메서드를 적용하였다.
지금 상태로는 서비스는 그냥 불필요한 단계를 거치는 느낌일 뿐이다. 하지만 따라가다보면 서비스의 역할을 알게된다고 한다.

```ts
import { MessagesRepository } from "./messages.repository";

export class MessagesService {
  messagesRepo: MessagesRepository;

  constructor() {
    // 서비스가 자체적인 의존성을 가지고 있습니다.
    // 실제 앱에서 이렇게 하지 마십시오.
    this.messagesRepo = new MessagesRepository();
  }

  findOne(id: string) {
    return this.messagesRepo.findOne(id);
  }

  findAll() {
    return this.messagesRepo.findAll();
  }

  create(content: string) {
    return this.messagesRepo.create(content);
  }
}
```

### 26. 컨트롤러 수동 테스트

`messages.json` 파일에 빈 객체 추가하여, 의도한 코드가 작동하도록 한다.

`messages.controller.ts` 컨트롤러에 서비스를 연결한다

```ts
import { Body, Controller, Get, Param, Post } from '@nestjs/common';
import { CreateMessageDto } from './dtos/create-message.dto';
import { MessagesService } from './messages.service';

@Controller('messages')
export class MessagesController {
  messagesServices: MessagesService;

  constructor() {
    // 실제 앱에서 이렇게 하지 마세요.
    // 의존성 주입을 사용하세요.
    this.messagesServices = new MessagesService();
  }

  @Get()
  listMessages() {
    return this.messagesServices.findAll();
  }

  @Post()
  createMessage(@Body() body: CreateMessageDto) {
    return this.messagesServices.create(body.content);
  }

  @Get('/:id')
  getMessage(@Param('id') id: string) {
    return this.messagesServices.findOne(id);
  }
}
```

그리고 `request.http` 요청 테스트를 통해 데이터가 `messages.json` 파일에 잘 기록되고
전체 및 id 조회도 잘 작동하는 것을 확인할 수 있다.

### 27. Nest에 내장된 예외 라이브러리로 오류 보고하기

id에 해당하는 메시지를 찾을 때 현재는 데이터가 없더라도 정상적인 응답인 HTTP 상태 코드 200을 응답 하고있다. 찾는 데이터가 없을 때는 404 찾을 수 없음을 응답으로 바꾼다.

`nestjs/common`의 `NotFoundException` 클래스를 사용하여 메시지가 없을 때에 대한 예외 처리를 컨트롤러에 작성하였다.
이외에도 다양한 HTTP 상태 코드에 대한 클래스를 제공한다.

```ts
// messages.controller.ts
{
  ...
  @Get('/:id')
  async getMessage(@Param('id') id: string) {
    const message = await this.messagesServices.findOne(id);

    if (!message) {
      throw new NotFoundException('message not found.');
    }

    return message;
  }
}
```

```http
HTTP/1.1 404 Not Found
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 69
ETag: W/"45-z7TheIgAfnLt/M3BoT+vz5tmnZk"
Date: Thu, 19 Jun 2025 14:55:19 GMT
Connection: close
{
  "message": "message not found.",
  "error": "Not Found",
  "statusCode": 404
}
```

### 28. 제어 역전 자세히 알아보기

파이프 -> 메시지 컨트롤 -> 메시지 서비스 -> 메시지 리포지토리
의존성으로 연결되어 있다.

제어 역천 원칙에 의해
클래스 안에서 클래스 자체적인 의존성 생성을 하지 말아야 한다. (원본 생성)
생성자에서 매개변수로 의존성을 주입 받아야 한다. (사본 생성)
더 나아가 서비스에서 리포지토리 인터페이스를 의존성을 주입 받는다.

소스코드로 보면 다음과 같다.

```ts
// Bad, 클래스를 직접 생성하므로 강하게 결합되어 있고, 테스트가 교체가 어렵다.
export class MessagesService {
  messagesRepo: MessagesRepository;
  constructor() {
    this.messagesRepo = new MessagesRepository();
  }
}

// Better, 제어 역전 적용, 생성자의 매개변수로 클래스 의존성 주입
export class MessagesService {
  messagesRepo: MessagesRepository;
  constructor(repo: MessagesRepository) {
    this.messagesRepo = repo;
  }
}

// Best, 생성자의 매개변수로 인터페이스 의존성 주입
interface Repository {
  findOne(id: string);
  findAll();
  create(content: string);
}
export class MessagesService {
  messagesRepo: Repository;
  constructor(repo: Repository) {
    this.messagesRepo = repo;
  }
}
```

> **제어 역전(Inversion of Control, IoC)**
> 제어 역전이란 객체의 제어권을 외부로 위임하는 원칙입니다.
> 전통적인 방식에서는 객체나 함수가 직접 필요한 의존성을 생성하고 제어합니다.
> 반면, IoC에서는 이 제어권을 외부(프레임워크, 상위 객체 등)로 넘깁니다.
> 일반 방식: “요리사가 식재료를 직접 시장에서 사고 요리”
> IoC 방식: “누군가 식재료를 요리사에게 주고 요리만 하게 함”
> 제어 역전 원칙으로 의존성을 외부에서 주입하게 되면, 의존성 교체가 쉬워지고, Mock 객체나 Stub으로 테스트가 가능하고, 기능 추가/변경 시 기존 코드 수정을 최소화할 수 있습니다.

### 29. 의존성 주입 소개

NestJS의 의존성 주입 컨테이너는 객체를 생성하고 관리하며, 필요한 곳에 자동으로 주입해주는 기능을 수행한다.

클래스와 의존성들 대한 리스트를 기록하는 곳과 인스턴스들을 생성하여 리스트에 기록하는 곳이 나누어져 있다.

기본적으로 의존성 주입에서 사본을 생성하는 방법으로 `싱글톤 패턴`을 사용한다.

### 30. 의존성 주입을 이용하기 위한 리팩터링

`Injectable` 데코레이터를 이용하여 의존성 클래스를 등록한다.

```ts
// Repository
import { Injectable } from '@nestjs/common';
@Injectable() // 의존성 클래스로 사용하겠다는 의미
export class MessagesRepository { ... }

// Services
import { Injectable } from "@nestjs/common";
@Injectable()
export class MessagesService {
  // MessagesRepository를 의존성 주입
  constructor(public messagesRepo: MessagesRepository) {}
  ...
}
```

`module`에서 `providers`는 의존성 클래스들의 목록을 의미한다. 그리고 등록된 의존성들은 일반적으로 싱글톤 패턴에 의해 생성된다.(하나의 인스턴스만 생성된다)

```ts
// Module
import { Module } from '@nestjs/common';
import { MessagesController } from './messages.controller';
import { MessagesService } from './messages.service';
import { MessagesRepository } from './messages.repository';

@Module({
  controllers: [MessagesController],
  providers: [MessagesService, MessagesRepository], // 의존성 클래스 등록
})
export class MessagesModule {}
```

### 31. 의존성 주입에 관한 추가 참고사항

NestJS에서는 이전 제어 역전에서 다룬 일반적인 의존성 주입을 사용하고 있다.

Best에 소개된 인터페이스 의존성 주입하는 방식은 타입스크립트 특성에 따른 제약이 있어서 일반적으로 사용하지 않는다고 설명한다. 방법이 있지만 일반적이지 않아서 나중에 다룬다고 한다.

의존성 주입을 하기 위한 행위들이 불필요하고 불편하게 느낄지 몰라도, 나중에 테스트할 떄 큰 도움이 될 것이라고 설명한다.

---
