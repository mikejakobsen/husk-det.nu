# Project Nearly Mean Stack Chat

Source: https://github.com/mikejakobsen/meanchat/tree/Date-fix

## To do

- [x] npm install && bower install
- [ ] redis-server
- [ ] Production: Gulp watch
- [ ] For viewing pleasure only, run: nodemon
- [ ] http://localhost:3000

Info: For at gøre projektet, kræves en server instans af [Redis](http://redis.io)

Redis installeres via `Brew install redis` eller via wget:

```bash
	$ wget http://download.redis.io/redis-stable.tar.gz:
	$ tar xvzf redis-stable.tar.gz
	$ cd redis-stable
	$ make
```

Opgave beskrivelse: http://www.mikejakobsen.com/stuff/mandatory-1.pdf

## Opbygning

```
.
├── README.md
├── app -> Base directory backend
│   ├── auth
│   │   └── index.js -> Authentification
│   ├── config
│   │   ├── config.json -> Config file containing Facebook/Twitter/Redis tokens
│   │   └── index.js
│   ├── database
│   │   ├── index.js -> DB connection and Schema import
│   │   └── schemas
│   │       ├── message.js -> Message schema
│   │       ├── room.js -> Room schema
│   │       └── user.js -> User schema
│   ├── logger
│   │   └── index.js -> Winston logger
│   ├── models
│   │   ├── message.js -> Message model
│   │   ├── room.js -> Room model
│   │   └── user.js -> User model
│   ├── routes
│   │   └── index.js -> Routing
│   ├── session
│   │   └── index.js -> Connect-Mongo sessions
│   ├── socket
│   │   └── index.js -> Socket.io functionality
│   └── views
│       ├── chatroom.ejs -> Chatroom view
│       ├── head.ejs -> HTML header
│       ├── login.ejs -> Login view
│       └── rooms.ejs -> Room list view
├── bower.json -> Bower config
├── debug.log -> Winston logs
├── docs
│   └── README.md
├── gulpfile.js -> Gulptasks
├── package.json -> NPM config/ import modules
├── server.js -> Node.js server instance
├── src
│   ├── img
│   │   └── user.jpg
│   ├── js
│   │   ├── login.js
│   │   ├── main.js
│   │   └── rooms.js
│   └── styles
│       ├── app.sass
│       └── variables.sass
├── static -> Gulp compiled frontend files
│   ├── css
│   │   └── app.css
│   ├── img
│   │   ├── user.jpg
│   │   └── user.png
│   └── js
│       ├── login.js
│       ├── main.js
│       └── rooms.js
└── structure.md

20 directories, 38 files
```

## Mean Stack

### Express

[Express.js](http://expressjs.com) er et `Web application framework` baseret på Node.js.

Express er et relativt minimalt, og gør derfor i stor grad brug af moduler. Som fx. de anvendte [Socket.io](https://github.com/socketio/socket.io) og [Mongoose](http://mongoosejs.com).

Node.js funktionen `require()` der gør det muligt at importere moduler.

Ikke i global namespace, modulet får derimod sit eget scope. De enkelte aspekter af modulet, som senere er benyttet, skal derfor eksporteres.

Et eksempel funktionaliteren tilknyttet de enkelte [chat rum](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/models/room.js#L134). For at skabe disse funktionalitet benyttes modulerne.[Mongoose](https://github.com/Automattic/mongoose) og [Logger/Winston](https://github.com/winstonjs/winston), men da disse moduler ikke benyttes direkte i disse funktioner, men blot variabler og funktionalitet udledt af disse moduler. Eksporteres [roomModel](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/room.js#L32) og [userModel](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/user.js#L87) der er udledt af [Mongoose funktionaliter](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/user.js#L4). Dermed holdes modulerne som helhed ude af det globale namespace. De færdige værdier bliver udelukkende eksporteret, imellem de enkelte aspekter af applikationen.



## Social Login

Dependencies:

* [Passport](https://github.com/jaredhanson/passport-facebook)
* [Passport Local](https://github.com/jfromaniello/passport.socketio)
* [Passport Twitter](https://github.com/jfromaniello/passport.socketio)
* [Passport Facebook](https://github.com/jfromaniello/passport.socketio)


Når brugeren indledningsvis tilgår applikationen, og dermed `/` tilgår han ligeledes nedenstående conditional statement.

Der i tilfældet at den besøgende har en instance af [IsAuthenticated](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/models/user.js#L55) bliver ledt videre til `/Rooms`. Ellers tilgår brugeren `/login` med tilhørende [Connect-Flash](https://github.com/jaredhanson/connect-flash) statusmeddelelse. Der enten viser en viser eventuelle fejlmeddelelser, eller leder den besøgende videre til `showRegisterForm`.

```javascript
// Index route
router.get('/', function(req, res, next) {
	// If isAuthenticated redirect
	// til rum oversigten
	// /rooms
	if(req.isAuthenticated()){
		res.redirect('/rooms');
	}
	else{
		// Render login siden
		// req.flash
		res.render('login', {
			success: req.flash('success')[0],
			errors: req.flash('error'),
			showRegisterForm: req.flash('showRegisterForm')[0]
		});
	}
});
```
## Oprettelse af brugeren

I tilfælde af at brugeren ønsker at oprette sig via `credentials`. Poster han indledningsvis to variabler der tilgåes i `request bodyen`, og gemmes i credentials variablen.
Denne variable tjekkes dernæst for indhold, i tilfældet at variablen er tom, og brugeren dermed intet har skrevet. Tilgåer brugeren en fejlmeddelelse via [Connect-flash](https://github.com/mikejakobsen/meanchat/blob/a48ee2fcccf62e3fe307ae5e241b9ff7c8b6c834/server.js#L8) middlewaren. Hvis conditional statementen derimod evalueres til false, tjekkes brugerens indtastninger op imod databasens [Users collection](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/user.js#L24).
Findes brugeren, tilgår den besøgende igen en fejlmeddelelse. Evalueres den derimod til false. Laves en instance af [UserSchema](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/user.js#L24), der dermed gemmer brugeren i databasen.


```javascript
// Create user
router.post('/register', function(req, res, next) {

	// Post variablerne req.body.username og req.body.password
	// req.body - requst body
	var credentials = {'username': req.body.username, 'password': req.body.password };

	// If username === '' ingenting samt password
	// Flash error
	// Hent showRegisterForm
	if(credentials.username === '' || credentials.password === ''){
		req.flash('error', 'Write something, dammit..');
		req.flash('showRegisterForm', true);
		res.redirect('/');
	}else{

		// Tjek om brugeren findes
		User.findOne({'username': new RegExp('^' + req.body.username + '$', 'i'), 'socialId': null}, function(err, user){
			if(err) throw err;
			if(user){
				req.flash('error', 'Du findes allerede');
				req.flash('showRegisterForm', true);
				res.redirect('/');
			}else{
				User.create(credentials, function(err, newUser){
					if(err) throw err;
					req.flash('success', "Yea' du er oprettet");
					res.redirect('/');
				});
			}
		});
	}
});
```
Brugeren modeleres dernæst fra UserSchema [user.js](./app/database/schemas/user.js#L24).

```javascript
var UserSchema = new Mongoose.Schema({
	username: { type: String, required: true, unique: true, dropDups: true},
	// Default: null da social ikke behøver password
	// og alm bruger ikke behover socialId
	password: { type: String, default: null },
	socialId: { type: String, default: null },
	picture:  { type: String, default:  DEFAULT_USER_PICTURE}
});

	if(!user.picture){
		user.picture = DEFAULT_USER_PICTURE;
	}
```
Og gemmes dernæst i databasen, i henhold til userschemaet. Den angivne kode encrypteres [andetsteds](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/user.js#L55) via [bCryptjs](https://github.com/kelektiv/node.bcrypt.js) og Blowfish algoritmen.

```javascript
{
    "_id" : ObjectId("57f5254ebdea2efa75055256"),
    "username" : "Sarah Frost",
    "picture" : "/img/user.jpg",
    "socialId" : null,
    "password" : "$2a$10$/JWVwNGlfozx8DauFwnnZeSvZXBmxGbOdM.gm7NK2rUfVZRBFqy12",
}
```
## Encryption

[bcrypt-nodejs](https://github.com/kelektiv/node.bcrypt.js)

I tilfælder som dette, hvor brugeren eventuelt angiver et personligt password, er kryptering af dette password vigtigt.
For at passwordet ikke bare kan aflæses direkte fra databasen, hvis udefrakommende skulle få adgang til denne.

For at opnå dette benyttes [bcrypt-nodejs](https://github.com/kelektiv/node.bcrypt.js     ), der via [Blowfish algoritmen](http://blowfish.css)krypterer passwordet.

Et [bcrypt](https://en.wikipedia.org/wiki/Bcrypt) krypteret password kendetegnes ved begyndelsen "$2a$".

Denne funktionalitet er en del af [userModel](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/user.js#L55), da bcrypt benyttes, både ved oprettelse, login samt evt. ændring af koden senere hen.

For at kryptere passwordet, benyttes to function calls, et indeholde antal `saltRounds`, der definerer hvor stærk krypteringen skal være.
Samt det ukrypterede password.

```javascript
bcrypt.genSalt(saltRounds, function(err, salt) {
    bcrypt.hash(myPlaintextPassword, salt, function(err, hash) {
        // Gem password
    });
});
```

```javascript
	bcrypt.genSalt(SALTROUNDS, function(err, salt) {
		if (err) return next(err);

		// Kør hash på user.password, med det salt vi lige lavede
		bcrypt.hash(user.password, salt, null, function(err, hash) {
			if (err) return next(err);

			// overskriv user.password med hash værdien
			// der gemmes.
			user.password = hash;
			next();
		});
	});
```

Denne funktion overskriv dermed [user.password](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/user.js#L64) variablen, og krypterer dermed det indtastede password.


For at application dernæst kan tjekke det ukrypterede password, og imod det krypterede gemte password, kaldes [bcrypt.compare](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/user.js#L77).  Der i tilfældet at det indtastede password matcher, det i databasen. Videregiver callbacket `isMatch`. Der slutteligt valideres i [Authentification](https://github.com/mikejakobsen/meanchat/blob/fe3b2b08f7df79635ee9534c2fb00e84119c43e3/app/auth/index.js#L45) delen af applikationen.

```javascript
// Load hash fra databasen
bcrypt.compare(myPlaintextPassword, hash, function(err, res) {
    // res == true
});
bcrypt.compare(someOtherPlaintextPassword, hash, function(err, res) {
    // res == false
});
```

```javascript
UserSchema.methods.validatePassword = function(password, callback) {
	bcrypt.compare(password, this.password, function(err, isMatch) {
		if (err) return callback(err);
		callback(null, isMatch);
	});
};
```

## Time since function

For at konventere tidsangivelserne i chatten fra SI tidsangivelse, til relativ tidsangivelse benyttes [Moment.js](http://momentjs.com/docs/). Indledningsvis importeres Moment.js i [node-serveren](https://github.com/mikejakobsen/meanchat/blob/Date-fix/server.js#L10).

```javascript
var moment = require('moment');
moment().format();
```

Moment.js laver en wrapper omkring `Date` object. For at tilgå denne wrapper kaldes `moment()` med en dato som parameter.
For at ændre tidsangivelserne i beskederne, kaldes `moment(message.date)` derfor, samt [fromNow()](https://github.com/moment/momentjs.com/blob/b1e718bc2cff461db34214992c79ba4054fa5d33/docs/moment/04-displaying/02-fromnow.md). Som en del af en EJS template string, ser det således ud.

```javascript
<%= moment(message.date).fromNow() %>
```
[EjS og Moment.js](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/views/chatroom.ejs#L49)


## Mongoose

Mongoose er en (ODM) `object data modeling`, der gør det muligt at fastsætte en [datamodel](https://github.com/mikejakobsen/meanchat/tree/Date-fix/app/database/schemas), og dermed en struktur i den gemte data.

Kontra en `ORM` som fx. [Laravel Elequent](https://laravel.com/docs/master/eloquent). Der `mapper` SQL-syntaxen så PHP kan interagere med SQL. Behøver en `ODM` som Mongoose ikke redefinere forholdet imellem database og applicationen, da `JSON` og `BSON` interager gnindningsfrit.


## EJS

EJS benyttes som `template engine`. En template engine gør det muligt at benytte statiske filer som `html`, da template enginen tilføjer eventuel dynamisk indhold ved runtime, og sender disse til clienten. Express understøtter en række [template engines](https://github.com/expressjs/express/wiki#template-engines). For at bibeholde den grundlæggende `html` struktur benyttes [EJS](http://www.embeddedjs.com/getting_started.html), der som navnet antyder `Èmbedded JavaScript`, tillader fx at frekventere et `ForLoop`, der tillader applicationen  at iterere over de gemte beskeder for dernæst at printe de [sendte beskeder](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/views/chatroom.ejs#L45).

```ejs
<% room.messages.forEach(function(message) { %>
	<div class="message-data">
		<span class="message-data-name"><%= message.username %></span>
		<span class="message-data-time"><%= message.date %></span>
	</div>
	<div class="message my-message" dir="auto"><%= message.content %></div>
<% }); %>
```

I forhold til de `runtime` sendte beskeder fra [Socket.io](https://github.com/socketio/socket.io), printes disse via JavaScript. Da EJS ikke tillader at tilføje elementer til applications DOM [løbende](https://github.com/mikejakobsen/meanchat/blob/Date-fix/src/js/main.js#L146). Disse beskeder appendes dernæst til [.chat history ul](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/views/chatroom.ejs#L43). Med hjælp fra Jquery.

```javascript
addMessage: function(message, users){
	message.date      = (new Date(message.date)).toLocaleString();
	message.username  = this.encodeHTML(message.username);
	message.content   = this.encodeHTML(message.content);


	var html = `<li class="clearfix">
		<div class="message-data align-right">
		<span class="message-data-time" >${message.date}</span> &nbsp; &nbsp;
	<span class="message-data-name" >${message.username}</span>
		</div>
		<div class="message other-message float-right">
		${message.content}
	</div>
		</li>`;
}

$(html).hide().appendTo('.chat-history ul').slideDown(200);

}
```

## Gulp

https://github.com/mikejakobsen/meanchat/blob/Date-fix/gulpfile.js

Gulp er en Javascript task runner, der gør det muligt at automatisere visse monotome opgaver, under udviklingen.
Gulp benyttes bland andet til at [kompilere](https://github.com/mikejakobsen/meanchat/blob/Date-fix/gulpfile.js#L24) [SASS](https://github.com/mikejakobsen/meanchat/blob/Date-fix/src/styles/app.sass) til CSS. Samt at køre [BrowserSync](https://github.com/mikejakobsen/meanchat/blob/Date-fix/gulpfile.js#L78) under udviklingen, der automatisk `refresher` browseren, så ændrningerne vises løbende.

| Gulp task | Funktion                                                 |
|-----------|----------------------------------------------------------|
| Images    | Komprimerer og eksporterer billeder                      |
| Styles    | Compiler SASS, Autoprefix og eksporter                   |
| Scripts   | Tjekker syntax, komprimerer og eksporterer `src/js`      |
| Hint      | Kører Jshint på backend                                  |
| Default   | Køres hvis man bare skriver Gulp - Kører styles, script. |
| Watch     | Kører styles, scripts, browsersync og en Nodemon server. |

Indledningsvis defineres en `Gulp.task`, som i eksemplet herunder med `scripts`.
Dernæst defineres input filerne `gulp.src('src/js/**/*.js')`. I dette tilfælder omfatter det alle JavaScript filerne, og dermed filerne med endelsen `.js` i src mappen. Stjernen/asterisk symbolet er her wildcards.
Dernæst tilføjes de omfattede filer til `Gulps stream`, hver `.pipe` instancierer dernæst et modul der er importeret i [Gulpfilen](https://github.com/mikejakobsen/meanchat/blob/Date-fix/gulpfile.js). Som fx. `.pipe(uglify())` der minificerer den importerede kode.

For dernæst at compilede, de importeredde filer til `pipe(gulp.dest('static/js/'))`. Og dermed `static/js/` mappen, der loades af applikationen.



```javascript
    gulp.task('scripts', function(){
        return gulp.src('src/js/**/*.js')
        .pipe(plumber({
            errorHandler: function (error) {
                console.log(error.message);
                this.emit('end');
            }}))
            .pipe(jshint())
            .pipe(beautify({indentSize: 2}))
            .pipe(jshint.reporter('default'))
            .pipe(babel())
            .pipe(uglify())
            .pipe(gulp.dest('static/js/'));
    });
```

## Data modeling

For at få applikationen til at agere dynamisk med brugeren, benyttes [MongoDB](https://www.mongodb.org) til at forankre dataen.

MongoDB er baseret på BSON, der er binary form for JSON, og dermed en seralisering af JSON. Fordelen ved at benytte BSON og dermed også JSON som primær datakilde, er at JSON basalt set er et Javascript object, og dermed arbejder gnindningsfrit med en Javascript baserede applikation som denne.

I forhold til brugerne af applikationen, vil de derfor blive oprettet i en `Users` collection.

Den nedenstående query på denne collection kan køres direkte fra din shell, og vil returnere. Et array af JSON objekter lignende det nedenstående, indholdende alle brugerne.

```javascript
mongo --quiet mikejakobsenchat --eval 'printjson(db.users.find().toArray())'
```
### Users collection
```javascript
[
	{
	"_id" : ObjectId("57f6bca8db17a743faf9e248"),
	"username" : "Mike Jakobsen",
	"picture" : "https://scontent.xx.fbcdn.net/v/t1.0-1/c13.0.50.50/p50x50/312809_2005127862915_182821112_n.jpg?oh=9571576a98c9983a914fed90faf837f1&oe=58AA3242",
	"socialId" : "102055468971868xx",
	"password" : null,
	}
]
```

I forhold til chatrummene og beskederne tilknyttet disse chatrum. Oprettes disse i en separat collection kaldet `Rooms`.

Kontrær en SQL baseret datamodel, er disse beskeder nested i `Rooms` collectionen. Da de er tilknyttet som elementer af det enkelte chatrum. 

```javascript
mongo --quiet mikejakobsenchat --eval 'printjson(db.rooms.find().toArray())' > rooms.json
```

En query gemt i filen [rooms.json](https://github.com/mikejakobsen/meanchat/blob/Date-fix/dummy-data/rooms.json) vil derfor returnere et rum der har et `userId` samt et `socketId` tilknyttet, der tilkendegiver de brugere der er til stedet i chatrummet. 

Samt et tomt [messages-array](https://github.com/mikejakobsen/meanchat/blob/Date-fix/dummy-data/rooms.json#L5). Der senere vil indeholde eventuelle beskeder.

```javascript
[
	{
		"_id" : ObjectId("57f781cbbb48c262d41c26d2"),
		"title" : "Chat",
		"messages" : [ ],
		"connections" : [
			{
				"userId" : "57f77a97bb48c262d41c26d1",
				"socketId" : "/chatroom#79Md-MM96uFdvJUoAAAC",
				"_id" : ObjectId("57f781cdbb48c262d41c26d3")
			}
		]
	}
]
```
### Messages

```
mongo --quiet mikejakobsenchat --eval 'printjson(db.rooms.find({title: "Chat"}).toArray())' > messages.json
```

En query på beskeder tilknyttet chat rummet, vil derfor [returnere](https://github.com/mikejakobsen/meanchat/blob/Date-fix/dummy-data/messages.json). 
Her ses `messages arrayet` der i stedet for blot at være et tomt, indeholder to objekter. 

Med beskedens indhold, afsenders username, en timestamp same et unikt ObjectId.

```javascript
[
	{
		"_id" : ObjectId("57f781cbbb48c262d41c26d2"),
		"title" : "Chat",
		"messages" : [
			{
				"content" : "Hej Mike",
				"username" : "Sarah",
				"_id" : ObjectId("57f786966bc0bf892003e7aa"),
				"date" : ISODate("2016-10-07T11:27:18.387Z")
			},
			{
				"content" : "Hej du",
				"username" : "Mike Jakobsen",
				"_id" : ObjectId("57f7869d6bc0bf892003e7ab"),
				"date" : ISODate("2016-10-07T11:27:25.164Z")
			}
		],
		"connections" : [
			{
				"userId" : "57f77a97bb48c262d41c26d1",
				"socketId" : "/chatroom#79Md-MM96uFdvJUoAAAC",
				"_id" : ObjectId("57f781cdbb48c262d41c26d3")
			},
			{
				"userId" : "57f786296bc0bf892003e7a6",
				"socketId" : "/chatroom#vg6jra-g0KlGrDVHAAAS",
				"_id" : ObjectId("57f786f46bc0bf892003e7b0")
			}
		]
	}
]

```

### Data Schema / Mongoose

Kontrær SQL kræver en Non-Relational database som MongoDB ikke et predefined schema. 

I tilfældet med denne applikation har vi dog valgt at predefinere schemaer, via Mongoose.
For på denne vis at sikre, at kun den specificerede data, kan indsættes i de pågældende objekter i databasen.

Herunder ses det definerede schema for `Users` collectionen.
Grundet muligheden for både at oprette en bruger via Facebook/Twitter, samt via generisk bruger oprettelse.
Indeholde dette [UserSchema](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/user.js) dog en hvis fleksibilitet. Da et Login via Facebook/Twitter kræver et Username, picture samt [socialId](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/user.js#L29) er username defineret til at required, unikt samt eventuelle duplikerede username entiteter ikke er tilladt. Da `socialId` kun benyttes ved oprettelse via disse tjenester er denne value defineret til at være null som udgangspunkt. Ligeledes benyttes password ikke her, så denne værdi er ligeledes defineret til null, da en mangel på denne værdi stadig genererer et validt bruger objekt.

I tilfældet med brugeroprettelse udelukkende ved brug af username og password. Benyttes derfor `username`, `password` samt et generisk profil billede tilføjes ud fra konstanten [const DEFAULT_USER_PICTURE](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/user.js#L11) der agerer default værdi, så i tilfældet at `picture` ikke specificeres direkte, vil denne image-path blive tilføjet som værdi.

```javascript
    var UserSchema = new Mongoose.Schema({
        username: { type: String, required: true, unique: true, dropDups: true},
        // Default: null da social ikke behøver password
        // og alm bruger ikke behover socialId
        password: { type: String, default: null },
        socialId: { type: String, default: null },
        picture:  { type: String, default:  DEFAULT_USER_PICTURE}
    });
```

I forhold til `Rooms collectionen` består denne af to Schemas, det overliggende [RoomSchema](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/room.js#L21). Der her indeholder titlen på rummet, samt et array med de forbundne brugere. Samt edet nestede schema [MessageSchema](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/room.js#L15) der specificerer indholdet af det underliggende [MessagesSchema](https://github.com/mikejakobsen/meanchat/blob/Date-fix/dummy-data/messages.json#L5).

Dette schema indeholder ligeledes titel validering. Via et [Regex pattern](https://github.com/mikejakobsen/meanchat/blob/Date-fix/app/database/schemas/room.js#L13) der specificerer at `title` skal starte med et `lowercase/uppercase` bogstav imellem A-Z i alfabetet. 

Denne validering tilføjes dernæst via [match](http://mongoosejs.com/docs/api.html#schema_string_SchemaString-match) funktionaliteten i Mongoose.

```javascript
    // Valider at rum navnet start med et bogstav A-Z
    var validateTitle = /[a-zA-Z]/;

    var MessageSchema = Mongoose.Schema({
        content: { type: String, required: true},
        date: { type: Date, default: Date.now },
        username: { type: String, required: true}
    });

    var RoomSchema = new Mongoose.Schema({
        // #Todo add more validation
        title: { type: String, required: true, match: validateTitle },
        connections: { type: [{ userId: String, socketId: String }]},
        messages: [MessageSchema]
    });
```


## Dependencies

* [Bcrypt-nodejs](https://www.npmjs.com/package/bcrypt-nodejs)
* [Body-parser](https://github.com/expressjs/body-parser)
* [Connect-flash](https://github.com/jaredhanson/connect-flash)
* [Connect-mongo](https://github.com/jdesboeufs/connect-mongo)
* [Ejs](https://github.com/mde/ejs)
* [Express](https://github.com/expressjs/express)
* [Express-session](https://github.com/expressjs/session)
* [Moment](http://momentjs.com/)
* [Mongoose](https://github.com/Automattic/mongoose)
* [Passport](https://github.com/jaredhanson/passport)
* [Passport-facebook](https://github.com/jaredhanson/passport-facebook)
* [Passport-local](https://github.com/jaredhanson/passport-local)
* [Passport-twitter](https://github.com/jaredhanson/passport-twitter)
* [Redis](https://github.com/antirez/redis)
* [Socket.io](https://github.com/socketio/socket.io)
* [Socket.io-redis](https://github.com/socketio/socket.io-redis)
* [Winston](https://github.com/winstonjs/winston)
