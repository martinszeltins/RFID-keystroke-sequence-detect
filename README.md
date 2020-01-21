> Author: Martins Zeltins
> Date: 2020-01-21
>
> This application listens for every key press and
> tries to detect when an RFID tag has been scanned.
> When an RFID tag is scanned, there is less than 25 ms
> between each keystroke. So when we detect that between
> two keystrokes there has been a less than a 25 ms delay,
> we start adding them to our result string (keysPressed),
> otherwise, we clear it an start over again.
>

````
const ioHook = require('iohook')
let currentTime = 0
let timeBetween = 0
let keysPressed = ''
let timesAdded = 0

let lastKeysPressed = [
	{ key: '', time: 0 },
	{ key: '', time: 0 }
]

function getKeyChar(rawcode)
{
	if (rawcode == 13) {
		return 'Enter'
	} else {
		return String.fromCharCode(rawcode)
	}
}

ioHook.on("keydown", event => {

	currentTime = Date.now()

	lastKeysPressed[0] = lastKeysPressed[1]

	lastKeysPressed[1] = {
		key: getKeyChar(event.rawcode),
		time: currentTime,
	}

	timeBetween = lastKeysPressed[1].time - lastKeysPressed[0].time

	if (timeBetween <= 25 && timesAdded === 0)
	{
		keysPressed += lastKeysPressed[0].key
		keysPressed += lastKeysPressed[1].key

		timesAdded++
	} else if (timeBetween <= 25 && timesAdded > 0) {
		keysPressed += lastKeysPressed[1].key

		if (keysPressed.includes('Enter')) {
			timesAdded = 0
		}
	} else {
		keysPressed = ''
		timesAdded = 0
	}

	console.log(keysPressed)
})

ioHook.start()
````
