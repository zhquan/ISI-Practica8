Plataphorma
===========

Meteor pet project created to teach my students the following Meteor functionality: 

* Collection's publish/subscribe 
* Deps.autorun 
* Meteor.methods/call 
* Integration of non-Meteor code in compatibility folder (HTML5 games Alien Invasion and Froot Wars)
* Usage of allow to control client access to collections

![ScreenShot](/screenshot.png)


Plataphorma offers the possibility to run 2 different HTML5 games: Alien Invasion and Froot Wars. 

On the right side of the screen the best players of each game are shown, updated in real time each time a signed in player finishes a game. If no game is selected, the best players overall are shown.

On the left side of the screen a chatroom for the current game is available. Only signed in users can post messages. If no game is selected a general chatroom is shown.

The original code of the two HTML5 games integrated in this project is available here:
* Alien Invasion: https://github.com/cykod/AlienInvasion
* Froot Wars: http://www.wrox.com/WileyCDA/WroxTitle/Professional-HTML5-Mobile-Game-Development.productCd-1118301323,descCd-DOWNLOAD.html

Bootstrap style (file bootstrap.min.css) provided by http://bootswatch.com


Running the project
-------------------

A live version of this code is running here: http://plataphorma.meteor.com

To run the project locally, clone the repo and run ```meteor``` inside it. You can see in .meteor/packages that this Meteor project uses these packages:
* ```meteor remove autopublish```
* ```meteor remove insecure```
* ```meteor add bootstrap```
* ```meteor add accounts-ui```
* ```meteor add accounts-password```

-------------------

1) Click en boton de juego
    index.html
    Al estar el puntero encima de la seleccion de juego se activa el 
            <div class="span7"> 
            {{> choose_game}}
    y va al template de choose_game.

    En client.js 
    En la linea 112: Template.choose_game.events = {..}
    Cuando le das click en alguno de los juegos irá a un juego u otro con los eventos 
            'click #AlienInvasion': function () {..} 
            'click #FrootWars': function () {..} 
            'click #none': function () {..}
    Si haces click a none ejecutará la linea 133, 145, 157
            Template.best_players.none = function (){..}
            Template.chat.none = function (){..}
            Template.best_players.best_players = function (){..}
    si no ejecutarán las lineas 137, 149, 157
            Template.best_players.gameName = function (){..}
            Template.chat.gameName = function (){..}
            Template.best_players.best_players = function (){..}

    index.html
    Dependiendo lo seleccionado en client.js se mostrará la linea 22 o 25
    Linea 22: 
            <div id='container' class="span7"> {{> alien_invasion}}
        Pintará el chat, la puntuación y el juego de Alien Invasion
    Linea 25:
            <div id="gamecontainer" class="span7"> {{> froot_wars}}  
        Pintará el chat, la puntuación y el juego de Froot Wars

    server.js
    Te devuelve los puntos y los mensajes que ya estan de antes.

 

2) Se escribe mensaje chat sin estar autenticado
    client.js
    Linea 84:
            Template.input.events = {
                'keydown input#message' : function (event) {
	            if (event.which == 13) { 
	                if (Meteor.userId()){..}
	                else {    --> Ejecuta esto porque no esta autentificado
		                $("#login-error").show();
	                }
	            }
                }
            }

    index.html
    Linea 60:
            <div class="alert fade in" id="login-error" style="display:none;">
                <button type="button" class="close">x</button>
                You must be signed in to post messages. ---> muestra este mensaje si intentas escribir
            </div>
        
3) Se escribe mensaje chat estando autenticado
    client.js
    Linea 84:
            Template.input.events = {
                'keydown input#message' : function (event) {
                if (event.which == 13) { 
                    if (Meteor.userId()){..} --> Ejecuta este if
                    else {..}
                }
                }
            }

4) Se termina partida con puntuacion mas alta sin estar autenticado
    server.js
    Linea 46:
        Meteor.methods({
            matchFinish: function (game, points) {
                if (this.userId)   
                    Matches.insert ({user_id: this.userId, 
                    time_end: Date.now(),
                    points: points,
                    game_id: game
                });
            }
        });
    Como no estas autenticado no entras en el if (this.userId) y no muestra tus puntos
        
5) Se termina partida con puntiacion mas alta estando autennticado
    server.js
    Linea 46:
        Meteor.methods({
            matchFinish: function (game, points) {
                if (this.userId)   
                    Matches.insert ({user_id: this.userId, 
                    time_end: Date.now(),
                    points: points,
                    game_id: game
                });
            }
        });
    Como estas autenticado no entras en el if (this.userId), muestra tus puntos con el nombre, fecha, puntos, juego.

6) ¿Que sale en consola cuando te autenticas(sign in)?
    En vez de Sign in te pone tu nombre de usuario.

7) ¿Que sale en consola cuanto sales(sign out)?
    Te vuelve a reaparecer el Sign in en vez de tu nombre de usuario y te desautentificas.
