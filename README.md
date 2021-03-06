# jsmodule

This is the javascript connector to the Atheios framework. After generating the game IDs on the portal
https://portal.atheios.org, You can initiate the connection and then

- Authenticate Your game
- Authenticate the user
- Set the wage
- Report the game score
- Request the ladder

Depending on the game settings then the backend identifies the winner and 
redistributes the waged Atheios

Note that the game cllient needs to include the three libaries

```
<script type="text/javascript" src="https://www.atheios.org/src/atheios.js"></script>
<script type="text/javascript" src="https://www.atheios.org/src/atheios-api.js"></script>
<script type="text/javascript" src="https://www.atheios.org/src/hmac-sha256.js"></script>
```

An example HTML is added where You can see the functionality and how to call the different function is attached also:

atheios_portal.htrml

```
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=US-ASCII">
<title>Atheios - JS implementation</title>
<script type="text/javascript" src="https://www.atheios.org/src/atheios.js"></script>
<script type="text/javascript" src="https://www.atheios.org/src/atheios-api.js"></script>
<script type="text/javascript" src="https://www.atheios.org/src/hmac-sha256.js"></script>

</head>
<body>
	<h1>Intro</h1>
	<b>Version 0.1.4</b><hr>
	This is a test page to show how the interaction with the Atheios portal is currently working.<br>
	In order to perform the test please enter Your private gametoken and gamesecret and set the connection to wss.atheios.org.
	The ones here set as default will not work for You.
	The token and secret can be generated on the portal <a href="https://portal.atheios.org">portal.atheios.org</a>.
	Create also a gaming user on <a href="https://play.atheios.org">play.atheios.org</a> and use it when logging in. Again the default values here
	will not work.<br>
	Step through the below items one by one to show the interaction with the portal. The code here can used for game development, but note that there
	will be API additions in the months to come. We will try to document things on <a href="https://docs.atheios.org">docs.atheios.org</a>.

	<h2>1. Authenticate Your game</h2>
	<p>
	Game token <input id="gametoken" value="M1yqo-958Fw-yakkn-nPF16-4SpZX"><br>
	Game secret <input id="gamesecret" value="3F9QYkgdraGLhCDJMiJpKQapAdGWJbzdFy3zKjGdXQxpOCv0co"><br>
	<br>
		<input type="radio" id="internet" name="local" value="false">
		<label for="internet">wss.atheios.org</label><br>
		<input type="radio" id="localhost" name="local" value="true">
		<label for="localhost">localhost</label><br>
	<button onClick="init();">Initalise</button>
	<div id="messages">
		<textarea rows="5" cols="100" id="messages_init"></textarea>
	</div>
	<br>
	<h2>2. Login</h2>
	<p>
		User name <input id="username" value="testuser"><br>
		User pass <input id="userpass" value="blabla0"><br>
	</p>
	<button onClick='atheios.authenticationRequest(document.getElementById("username").value, getElementById("userpass").value,  document.getElementById("gametoken").value, loginResponse)'>Login</button>
	<div id="messages">
		<textarea rows="5" cols="100" id="messages_login"></textarea>
	</div>

	<h2>3. Account details</h2>
	<p>
		AuthToken <input id="authToken1"><br>
		UserId <input id="userId1"><br>
	</p>
	<button onClick='atheios.accountDetailsRequest(document.getElementById("authToken1").value, document.getElementById("userId1").value, accountResponse)'>Account data</button>
	<div id="messages">
		<textarea rows="5" cols="100" id="messages_account"></textarea>
	</div>

	<h2>4. Wage/game start</h2>
	<p>
		AuthToken <input id="authToken2"><br>
		UserId <input id="userId2"><br>
		GameId <input id="gameId1"><br>
		Wage <input id="wage" value="1"><br>
	</p>
	<button onClick='atheios.setWageRequest(document.getElementById("authToken2").value, document.getElementById("wage").value, document.getElementById("gameId1").value, getElementById("userId2").value, document.getElementById("gametoken").value,wageResponse)'>Wage</button>
	<div id="messages">
		<textarea rows="5" cols="100" id="messages_wage"></textarea>
	</div>

	<h1>5. Game finish</h1>
	<p>
		AuthToken <input id="authToken3"><br>
		UserId <input id="userId3"><br>
		PlayId <input id="playId1"><br>
		Score <input id="score"><br>
	</p>

	<button onClick='atheios.finishGameRequest(document.getElementById("authToken3").value, getElementById("userId3").value,  document.getElementById("playId1").value, document.getElementById("score").value, gamefinishResponse)'>Login</button>
	<div id="messages">
		<textarea rows="5" cols="100" id="messages_gamefinish"></textarea>
	</div>
	<h2>6. Ladder</h2>
	<p>
		AuthToken <input id="authToken4"><br>
		UserId <input id="userId4"><br>
		PlayId <input id="playId2"><br>
		gameId <input id="gameId2"><br>
	</p>

	<button onClick='atheios.gameLadderRequest(document.getElementById("authToken4").value, getElementById("userId4").value,  document.getElementById("playId2").value, document.getElementById("gameId2").value, ladderResponse)'>Login</button>
	<div id="messages">
		<textarea rows="5" cols="100" id="messages_ladder"></textarea>
	</div>

</body>
<script type="text/javascript">

	//Create an atheios object to be used
	var atheios = new Atheios();
	//Initialse the SDK
	function init() {
		var local="false";
		console.log("Debug++++: %s", document.getElementById('localhost').value);
		if (document.getElementById('localhost').checked) {
			local="true";
		}

		atheios.initLive({
			key: document.getElementById('gametoken').value,
			secret: document.getElementById('gamesecret').value,
			local: local,
			onNonce: onNonce,
			onInit: onInit,
			onMessage: onMessage,
			logger: console.log,
		});
	}

	//Callback function to hmac sha256 a nonce with the secret. It's assumed you will have your own method of securing the secret;
	function onNonce(nonce) {
		return CryptoJS.enc.Base64.stringify(CryptoJS.HmacSHA256(nonce, document.getElementById('gamesecret').value));
	}
	
	//Callback to handle when the SDK is initialised and ready to go
	function onInit() {
		console.log("Initialised");
	}
	
	//Callback to handle async messages from the portal platform
	function onMessage(response) {
		console.log("onMessage");
		document.getElementById('messages_init').value=JSON.stringify(response);
	}



	function loginResponse(response) {
		console.log(JSON.stringify(response));
		document.getElementById('messages_login').value=JSON.stringify(response);
		document.getElementById('authToken1').value=response["authToken"];
		document.getElementById('authToken2').value=response["authToken"];
		document.getElementById('authToken3').value=response["authToken"];
		document.getElementById('authToken4').value=response["authToken"];
		document.getElementById('userId1').value=response["userId"];
		document.getElementById('userId2').value=response["userId"];
		document.getElementById('userId3').value=response["userId"];
		document.getElementById('userId4').value=response["userId"];
		document.getElementById('gameId1').value=response["gameId"];
		document.getElementById('gameId2').value=response["gameId"];
	}

	function wageResponse(response) {
		console.log(JSON.stringify(response));
		document.getElementById('messages_wage').value=JSON.stringify(response);
		document.getElementById('playId1').value=response["playid"];
		document.getElementById('playId2').value=response["playid"];
	}

	function accountResponse(response) {
		console.log(JSON.stringify(response));
		document.getElementById('messages_account').value=JSON.stringify(response);
	}

	function gamefinishResponse(response) {
		console.log(JSON.stringify(response));
		document.getElementById('messages_gamefinish').value=JSON.stringify(response);
	}

	function ladderResponse(response) {
		console.log(JSON.stringify(response));
		document.getElementById('messages_ladder').value=JSON.stringify(response);
	}


</script>
```

If You have any questions, join us on the Atheios Discord: https://discord.gg/D2TvDfz
 