var Game = pc.createScript('game');

Game.attributes.add('display', {
    type: 'asset',
    assetType: 'html'
});

Game.attributes.add('style', {
    type: 'asset',
    assetType: 'css'
});

// Create a script attribute to enable the drag and drop of a JSON asset containing character data
Game.attributes.add('characterData', {
    type: 'asset',
    assetType: 'json'
});

// initialize code called once per entity
Game.prototype.initialize = function() {
    this.initDisplay();

    var el;

    // Get JSON data from a project asset
    var characterData = this.characterData.resource;

    // Parse JSON data
    var names = this.parseCharacterData(characterData);

    // display character names
    el = document.querySelector("#character-name");
    el.textContent = names.join(", ");

    // display JSON data from asset
    el = document.querySelector("#asset-json");
    el.textContent = JSON.stringify(characterData, null, 4);

    // load JSON from a remote server
    this.loadJsonFromRemote("https://api.github.com/", function (data) {
        // display JSON data from remote server
        el = document.querySelector("#xhr-json");
        el.textContent = JSON.stringify(data, null, 4);
    });
};

Game.prototype.initDisplay = function () {
    var el = pc.createStyle(this.style.resource);
    document.head.appendChild(el);

    var div = document.createElement("div");
    div.setAttribute("id", "container");
    div.innerHTML = this.display.resource;
    document.body.appendChild(div);
};

Game.prototype.parseCharacterData = function (data) {
    var names = [];

    // Read the character data from the JSON asset return a list of names
    var characters = data.characters;
    for (var i = 0; i < characters.length; i++) {
        var character = characters[i];
        names.push(character.firstName + ' ' + character.lastName);
    }

    return names;
};

Game.prototype.loadJsonFromRemote = function (url, callback) {
    var xhr = new XMLHttpRequest();
    xhr.addEventListener("load", function () {
        callback(JSON.parse(this.response));
    });
    xhr.open("GET", url);
    xhr.send();
};
