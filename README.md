## google oauth

### install

```sh
yarn init

yarn add express mongoose connect-mongo express-session  express-handlebars dotenv method-override moment morgan passport passport-google-oauth20

yarn -D add nodemon cross-env
```

### APP 생성

### google cloud platform

- 새 프로젝트 생성
- API 및 서비스 > API 및 서비스 사용 설정 > GOOGLE+ API
- GOOGLE+ API > 사용자 인증 정보 > 사용자 인증 정보 만들기
- Oauth 동의 화면
- 사용자 인증 정보 만들기 > Oauth 클라이언트 ID
- 유형 : web application, 승인된 리디렉션 URI : http://localhost:3000/auth/google/callback

- client id => config.env

### passport

- passportjs => passport-google-oauth20

```ts
// app.js
const passport = require('passport');
const session = require('express-session');

// Passport config
require('./config/passport')(passport);

// Sessions
app.use(
  session({
    secret: 'keyboard cat',
    resave: false,
    saveUninitialized: false,
  })
);

// Passport middleware
app.use(passport.initialize());
app.use(passport.session());
```

```ts
// models/User.js
const mongoose = require('mongoose');

const UserSchema = new mongoose.Schema({
  googleId: {
    type: String,
    required: true,
  },
  displayName: {
    type: String,
    required: true,
  },
  firstName: {
    type: String,
    required: true,
  },
  lastName: {
    type: String,
    required: true,
  },
  image: {
    type: String,
  },
  createdAt: {
    type: Date,
    default: Date.now,
  },
});

module.exports = mongoose.model('User', UserSchema);
```

```ts
const GoogleStrategy = require('passport-google-oauth20').Strategy;
const mongoose = require('mongoose');
const User = require('../models/User');

module.exports = function (passport) {
  passport.use(
    new GoogleStrategy({
      clientID: process.env.GOOGLE_CLIENT_ID,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET,
      callbackURL: '/auth/google/callback',
    })
  );
};
```
