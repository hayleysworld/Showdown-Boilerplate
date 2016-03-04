'use strict';

const fs = require('fs');
const color = require('../config/color');

try {
	JSON.parse(fs.readFileSync('./config/db/cards/cards.json'));
} catch (e) {
	fs.writeFileSync('./config/db/cards/cards.json', '{}', 'utf8');
}
try { 
	JSON.parse(fs.readFileSync('./config/db/cards/usersCards.json'));
} catch (e) {
	fs.writeFileSync('./config/db/cards/usersCards.json', '{}', 'utf8');
}

exports.commands = {
	card: {
		add: function (target, room, user) {
			if (!this.can('eval')) return false;

			let cards = JSON.parse(fs.readFileSync('./config/db/cards/cards.json'));

			let rarityLevels = {
				'common': 1,
				'uncommon': 3,
				'rare': 5,
				'epic': 10,
				'legendary': 15
			};

			let opts = target.split(',');

			if (!opts[0] || !opts[1] || !opts[2] || !opts[3] || !opts[4]) return this.parse('/help card');

			let rarity = toId(opts[0]);
			let cardID = toId(opts[1]);
			let cardName = opts[2];
			let cardImage = opts[3];
			let points = rarityLevels[rarity];
			let packs = opts[4].split('|');

			let rarities = Object.keys(rarityLevels);

			if (rarities.indexOf(rarity) < 0) return this.errorReply('That is not a valid rarity level.');

			for (var i = 0; i < rarities.length; i++) {
				for (var j = 0; j < Object.keys(cards[rarities[i]]).length; j++) {
					if (Object.keys(cards[rarities[i]])[j] === cardID) return this.errorReply(cardID + ' is already present in ' + rarities[i] + '.');
				}
			}

			if (!cards[rarity]) cards[rarity] = {};

			if (!cards[rarity][cardID]) cards[rarity][cardID] = {};

				cards[rarity][cardID]['Card Name'] = cardName;
				cards[rarity][cardID]['Card Image'] = cardImage;
				cards[rarity][cardID]['Packs'] = [];

				for (var i = 0; i < packs.length; i++) cards[rarity][cardID]['Packs'].push(packs[i]);

				if (isNaN(points)) return this.errorReply('Points should be in numbers.');

				cards[rarity][cardID]['Points'] = points;

			fs.writeFileSync('./config/db/cards/cards.json', JSON.stringify(cards), 'utf8');

			this.sendReply('Card added successfully.');
		},

		remove: function (target, room, user) {
			if (!this.can('eval')) return false;

			let cards = JSON.parse(fs.readFileSync('./config/db/cards/cards.json'));

			if (!target) return this.parse('/help card');

			let cardID = toId(target);

			let rarities = Object.keys(cards);
			let rarityName = '';

			for (var i = 0; i < rarities.length; i++) {
				if (cards[rarities[i]][cardID]) rarityName += rarities[i];
			}

			if (rarityName.length === 0) return this.errorReply(cardID + ' is not a valid card.');

			delete cards[rarityName][cardID];

			if (Object.keys(cards[rarityName]).length === 0) delete cards[rarityName];

			fs.writeFileSync('./config/db/cards/cards.json', JSON.stringify(cards), 'utf8');

			this.sendReply('Card removed successfully.');

		},

		show: function (target, room, user) {
			if (!this.canBroadcast()) return;

			let cards = JSON.parse(fs.readFileSync('./config/db/cards/cards.json'));

			let cardID = toId(target);

			if (!target) return this.parse('/help card');

			let rarities = Object.keys(cards);
			let rarityName = '';

			for (var i = 0; i < rarities.length; i++) {
				if (cards[rarities[i]][cardID]) rarityName += rarities[i];
			}
	
			if (rarityName.length === 0) return this.errorReply(cardID + ' is not a valid card.');

			let cardImage = cards[rarityName][cardID]['Card Image'];
			let cardName = cards[rarityName][cardID]['Card Name'];
			let points = cards[rarityName][cardID]['Points'];
			if (points === 1 ? points += ' Point' : points += ' Points');
			let rarity = rarityName;
			let packs = cards[rarityName][cardID]['Packs'];

			let display = '';
			display += '<b><center>';
			display += '<font style="font-size: 15pt;">';
			display += cardName;
			display += '</font>'
			display += '</center></b>';
			display += '<br />';
			display += '<img style="border-radius: 10px; box-shadow: 2px 2px 5px black; position: relative; left: 300px;"';
			display += 'src="' + cardImage + '" width="150" height="200" title="' + cardID +'">';
			display += '<br />';
			display += '<font style="font-size: 13pt; font-weight: bold; position: relative; bottom: 150px; left: 50px;">';
			display += rarityName.charAt(0).toUpperCase() + rarityName.substr(1, rarityName.length);
			display += '</font>';
			display += '<br />';
			display += '<font style="font-size: 13pt; font-weight: bold; position: relative; bottom: 90px; left: 50px;">';
			display +=  points;
			display += '</font>';
			display += '<br />';
			display += '<font style="font-size: 10pt; font-weight: bold; position: relative; bottom: 20px; left: 10px;">';
			display += 'Found in packs: ' + packs.join(', ');
			display += '</font>';

			this.sendReply('|raw|<div style="background: #ffffff; border: 2px inset black; border-radius: 20px; padding: 5px;">'
							+ display 
							+ '</div>');
		},

		list: function (target, room, user) {
			if (!this.can('eval')) return false;

			let cards = JSON.parse(fs.readFileSync('./config/db/cards/cards.json'));

			let commonList = [];
			let uncommonList = [];
			let rareList = [];
			let epicList = [];
			let legendaryList = [];

			for (var i = 0; i < Object.keys(cards['common']).length;    i++) commonList.push(Object.keys(cards['common'])[i]);

			for (var i = 0; i < Object.keys(cards['uncommon']).length;  i++) uncommonList.push(Object.keys(cards['uncommon'])[i]);

			for (var i = 0; i < Object.keys(cards['rare']).length;      i++) rareList.push( Object.keys(cards['rare'])[i]);

			for (var i = 0; i < Object.keys(cards['epic']).length;      i++) epicList.push(Object.keys(cards['epic'])[i]);

			for (var i = 0; i < Object.keys(cards['legendary']).length; i++) legendaryList.push(Object.keys(cards['legendary'])[i]);

			let display = '';
			display += '<center><h2>Cards List</h2></center>';
			display += '<ul>';
			display += '<center><h3>Common List</h3></center>';
			display += '<li>' + commonList.join(', ')    +  '</li><br />';
			display += '<center><b>Amount Of Common Cards:</b> ';
			display += commonList.length;
			display += '<br />';			
			display += '<center><h3>Uncommon List</h3></center>';
			display += '<li>' + uncommonList.join(', ')  +  '</li><br />';
			display += '<center><b>Amount Of Uncommon Cards:</b> ';
			display += uncommonList.length;
			display += '<br />';
			display += '<center><h3>Rare List</h3></center>';
			display += '<li>' + rareList.join(', ')      +  '</li><br />';
			display += '<center><b>Amount Of Rare Cards:</b> ';
			display += rareList.length;
			display += '<br />';
			display += '<center><h3>Epic List</h3></center>';
			display += '<li>' + epicList.join(', ')      +  '</li><br />';
			display += '<center><b>Amount Of Epic Cards:</b> ';
			display += epicList.length;
			display += '<br />';
			display += '<center><h3>Legendary List</h3></center>';
			display += '<li>' + legendaryList.join(', ') +  '</li><br />';
			display += '<center><b>Amount Of Legendary Cards:</b> ';
			display += legendaryList.length;
			display += '<br /><br />';
			display += '<center><b>Total Amount Of Cards:</b> ';
			display += commonList.length + uncommonList.length + rareList.length + epicList.length + legendaryList.length;

			this.sendReply('|raw|<div class="infobox infobox-limited" style="background: white; border-radius: 5px;">'
							+ display
							+ '</div>');
		},

		give: function (target, room, user) {
			if (!this.can('forcewin')) return false;

			let cards = JSON.parse(fs.readFileSync('./config/db/cards/cards.json'));
			let usersCards = JSON.parse(fs.readFileSync('./config/db/cards/usersCards.json'));

			let opts = target.split(',');

			if (!opts[0] || !opts[1]) return this.parse('/help card');

			let targetId = toId(opts[0]);
			let cardID = toId(opts[1]);

			let rarities = Object.keys(cards);
			let rarityName = '';

			if (!usersCards[targetId]) usersCards[targetId] = {};
			if (!usersCards[targetId]['Cards']) usersCards[targetId]['Cards'] = [];
			if (!usersCards[targetId]['Points']) usersCards[targetId]['Points'] = 0;

			let usersCardsArray = usersCards[targetId]['Cards'];
			let points = 0;

			for (var i = 0; i < rarities.length; i++) {
				if (cards[rarities[i]][cardID]) rarityName += rarities[i];
			}

			if (rarityName.length === 0) return this.errorReply(cardID + ' is not a valid card.');

			usersCards[targetId]['Cards'].push(cardID);

			for (var i = 0; i < rarities.length; i++) {
				for (var j = 0; j < usersCardsArray.length; j++) {
					if (cards[rarities[i]][usersCardsArray[j]]) points += cards[rarities[i]][usersCardsArray[j]]['Points'];
				}
			}

			usersCards[targetId]['Points'] = points;

			fs.writeFileSync('./config/db/cards/usersCards.json', JSON.stringify(usersCards), 'utf8');

			this.sendReply('Card given successfully.');
		},

		take: function (target, room, user) {
			if (!this.can('eval')) return false;

			let cards = JSON.parse(fs.readFileSync('./config/db/cards/cards.json'));
			let usersCards = JSON.parse(fs.readFileSync('./config/db/cards/usersCards.json'));

			let opts = target.split(',');

			if (!opts[0] || !opts[1]) return this.parse('/help card');

			let targetId = toId(opts[0]);
			let cardID = toId(opts[1]);


			let rarities = Object.keys(cards);
			let rarityName = '';

			if (!usersCards[targetId]['Cards'] || usersCards[targetId]['Cards'].length === 0) return this.errorReply('User doesn\'t have any cards.');

			let usersCardsArray = usersCards[targetId]['Cards'];
			let points = 0;

			for (var i = 0; i < rarities.length; i++) {
				if (cards[rarities[i]][cardID]) rarityName += rarities[i];
			}

			if (rarityName.length === 0) return this.errorReply(cardID + ' is not a valid card.');

			let index = usersCards[targetId]['Cards'].indexOf(cardID);

			if (index < 0) return this.errorReply('User doesn\'t have that card.');

			usersCards[targetId]['Cards'].splice(index, 1);
		
			for (var i = 0; i < rarities.length; i++) {
				for (var j = 0; j < usersCards.length; j++) {
					if (cards[rarities[i]][usersCardsArray[j]]) points += cards[rarities[i]][usersCardsArray[j]]['Points'];
				}
			}

			usersCards[targetId]['Points'] = points;

			fs.writeFileSync('./config/db/cards/usersCards.json', JSON.stringify(usersCards), 'utf8');

			this.sendReply('Card taken successfully.');
		},

		transfer: function (target, room, user) {
			let opts = target.split(',');

			let cards = JSON.parse(fs.readFileSync('./config/db/cards/cards.json'));
			let usersCards = JSON.parse(fs.readFileSync('./config/db/cards/usersCards.json'));

			if (!opts[0] || !opts[1]) return this.parse('/help card');

			let targetId = toId(opts[0]);

			if (targetId === user.userid) return this.errorReply('You can not transfer cards to yourself.');

			let cardID = toId(opts[1]);

			let rarities = Object.keys(cards);
			let rarityName = '';

			if (!usersCards[user.userid]['Cards'] || usersCards[user.userid]['Cards'].length === 0) return this.errorReply('You don\'t have any cards.');

			if (!usersCards[targetId]) usersCards[targetId] = {};
			if (!usersCards[targetId]['Cards']) usersCards[targetId]['Cards'] = [];
			if (!usersCards[targetId]['Points']) usersCards[targetId]['Points'] = 0;

			let usersCardsArray = usersCards[user.userid]['Cards'];
			let usersCardsArrayOfReceiver = usersCards[targetId]['Cards'];
			let points = 0;
			let pointsOfReceiver = 0;

			for (var i = 0; i < rarities.length; i++) {
				if (cards[rarities[i]][cardID]) rarityName += rarities[i];
			}

			if (rarityName.length === 0) return this.errorReply(cardID + ' is not a valid card.');

			let index = usersCards[user.userid]['Cards'].indexOf(cardID);

			if (index < 0) return this.errorReply('You don\'t have that card.');

			usersCards[targetId]['Cards'].push(cardID);
			usersCards[user.userid]['Cards'].splice(index, 1);

			for (var i = 0; i < rarities.length; i++) {
				for (var j = 0; j < usersCardsArray.length; j++) {
					if (cards[rarities[i]][usersCardsArray[j]]) points += cards[rarities[i]][usersCardsArray[j]]['Points'];
				}

				for (var k = 0; k < usersCardsArrayOfReceiver.length; k++) {
					if (cards[rarities[i]][usersCardsArrayOfReceiver[k]]) pointsOfReceiver += cards[rarities[i]][usersCardsArrayOfReceiver[k]]['Points'];
				}
			}

			usersCards[user.userid]['Points'] = points;
			usersCards[targetId]['Points'] = pointsOfReceiver;

			fs.writeFileSync('./config/db/cards/usersCards.json', JSON.stringify(usersCards), 'utf8');

			this.sendReply('Card transferred successfully.');
		},

		showcase: function (target, room, user) {
			if (!this.canBroadcast()) return;

			let cards = JSON.parse(fs.readFileSync('./config/db/cards/cards.json'));
			let usersCards = JSON.parse(fs.readFileSync('./config/db/cards/usersCards.json'));

			if (!target) target = user.userid;
			target = toId(target);

			let display = '|raw|<div class="infobox infobox-limited" style="background: white; border: 2px inset black; border-radius: 5px;">';
			display += '<center><h2 style="color:' + color(target) + ';">' + target + '</h2><br />';
			
			
			let rarities = Object.keys(cards);
			let rarityName = '';
			
			if (!usersCards[target] || !usersCards[target]['Cards'] || usersCards[target]['Cards'].length === 0) return this.errorReply(target + ' does not have any cards.');

			for (var i = 0; i < usersCards[target]['Cards'].length; i++) {
				for (var j = 0; j < rarities.length; j++) {
					if (cards[rarities[j]][usersCards[target]['Cards'][i]]) rarityName += rarities[j];
				}

				let cardName = cards[rarityName][usersCards[target]['Cards'][i]]['Card Name'];
				let cardImage = cards[rarityName][usersCards[target]['Cards'][i]]['Card Image'];
				let cardPoints = cards[rarityName][usersCards[target]['Cards'][i]]['Points'];

				display += '<button name="send"';
				display += 'style="width: 100px; padding: 5px; background: transparent; border: none; border-radius: 10px; text-shadow: 0px 0px 8px black; outline: none;"';
				display += 'value="/card show ' + usersCards[target]['Cards'][i] + '">';
				display += '<img src="' + cardImage + '" title="' + usersCards[target]['Cards'][i] + '" style="box-shadow: 2px 2px 5px black;" width="60" height="80">';
				display += '<br />';
				display += '<center><b>' + cardName + '</b></center>';
				display += '</button>&nbsp;&nbsp;';
				
				if ((i + 1) % 3 === 0) display += '<br /><br />';

				rarityName = '';
			}

			let points = usersCards[target]['Points'];
			display += '<br /><b>Points: ' + points + '</b></center><br /></div>';

			this.sendReply(display);
		},

		ladder: function (target, room, user) {
			if (!this.canBroadcast()) return false;

			let usersCards = JSON.parse(fs.readFileSync('./config/db/cards/usersCards.json'));

			let allUsers = Object.keys(usersCards);

			if (!allUsers.length) return this.errorReply('The card ladder is empty!');

			let cardLadder = [];
			let points = {};
			let display = '<center><u><b>Card Ladder</b></u></center><br><table border="1" cellspacing="0" cellpadding="5" width="100%"><tbody><tr><th>Rank</th><th>Username</th><th>Points</th></tr>';

			for (var i in allUsers) {
	      		points[allUsers[i]] = usersCards[allUsers[i]]['Points'];
			}

			for (var name in points) {
				cardLadder.push([name, points[name]]);
			}

			cardLadder.sort(function(a, b) {
				return b[1] - a[1]
			});

			if (cardLadder.length < 10) {
				for (var i = 0; i < cardLadder.length; i++) {
					display += '<tr><td>' + (i + 1) + '</td><td>' + cardLadder[i].join('</td><td>') + '</td></tr>';
				}
			} else {
				for (var i = 0; i < 10; i++) {
					display += '<tr><td>' + (i + 1) + '</td><td>' + cardLadder[i].join('</td><td>') + '</td></tr>';
				}
			}

			display += '</tbody></table>';

			this.sendReplyBox(display);
		},

		'': function (target, room, user) {
			return this.parse('/help card');
		},
	},
	cardhelp: ['|raw|<center><h2>Cards</h2></center><br /><ul>' +
				'<li>/card give [rarity],[card id],[card name],[card image],[pack_1|pack_2|pack_3...] - Adds a new card to the cards database. Requires ~, &.</li><br />' +
				'<li>/card remove [card id] - Removes a card from the cards database. Requires ~, &.</li><br />' +
				'<li>/card show [card id] - Shows a card description from the cards database.</li><br />' +
				'<li>/card take [user name],[card id] - Takes the card from the user. Requires ~, &.</li><br />' +
				'<li>/card transfer [user name],[card id] - Transfers a card from your showcase to the other user.</li><br />' +
				'<li>/card showcase (user name) - Shows a users collection of cards.</li><br />' +
				'<li>/card ladder - Shows points ranking for cards.</li></ul>'],
};
