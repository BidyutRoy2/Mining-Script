# Mining Script


## Yes Coin

```bash
const styles = {
    success: 'background: #28a745; color: #fff; font-weight: bold; padding: 4px 8px; border-radius: 4px;',
    starting: 'background: #8640ff; color: #fff; font-weight: bold; padding: 4px 8px; border-radius: 4px;',
    error: 'background: #dc3545; color: #fff; font-weight: bold; padding: 4px 8px; border-radius: 4px;',
    info: 'background: #007bff; color: #fff; font-weight: bold; padding: 4px 8px; border-radius: 4px;'
};

const logPrefix = '%c[YesCoinBot by DefiWimar] ';
const originalLog = console.log;
console.log = function () {
    if (typeof arguments[0] === 'string' && arguments[0].includes('[YesCoinBot by DefiWimar]')) {
        originalLog.apply(console, arguments);
    }
};

console.error = console.warn = console.info = console.debug = () => {};

console.clear();
console.log(`${logPrefix}Starting`, styles.starting);

const waitForElement = (selector, timeout = 10000) => {
    return new Promise((resolve, reject) => {
        const intervalTime = 100;
        let timeElapsed = 0;
        const interval = setInterval(() => {
            const element = document.querySelector(selector);
            if (element) {
                clearInterval(interval);
                resolve(element);
            } else if (timeElapsed >= timeout) {
                clearInterval(interval);
                reject(new Error('Element not found'));
            }
            timeElapsed += intervalTime;
        }, intervalTime);
    });
};

const initializeScript = async () => {
    try {
        const container = await waitForElement('#coin-swipe-wrapper');
        console.log(`${logPrefix}Container found`, styles.success);

        const canvas = await waitForElement('#coin-swipe-wrapper canvas.sketch');
        console.log(`${logPrefix}Canvas found`, styles.success);

        const createMouseEvent = (type, x, y) => new MouseEvent(type, {
            bubbles: true,
            cancelable: true,
            clientX: x,
            clientY: y,
            screenX: x,
            screenY: y
        });

        const rect = canvas.getBoundingClientRect();
        const width = rect.width;
        const height = rect.height;

        const getRandomCoordinates = () => ({
            x: Math.random() * width + rect.left,
            y: Math.random() * height + rect.top
        });

        const getEnergyValue = () => {
            const energyElement = document.querySelector('.coin-pool');
            return energyElement ? parseInt(energyElement.textContent, 10) : 0;
        };

        const moveMouse = () => {
            if (getEnergyValue() < 25) {
                const pauseDuration = Math.random() * 30 + 30;
                console.log(`${logPrefix}Energy < 25, pausing for ${pauseDuration} seconds`, styles.info);
                setTimeout(moveMouse, pauseDuration * 1000);
                return;
            }

            const numMoves = 10;
            for (let i = 0; i < numMoves; i++) {
                const { x, y } = getRandomCoordinates();
                canvas.dispatchEvent(createMouseEvent('mousemove', x, y));
            }

            requestAnimationFrame(moveMouse);
        };

        moveMouse();

    } catch (error) {
        console.log(`${logPrefix}Error in YesCoin Collector script: ${error.message}`, styles.error);
    }
};

setTimeout(initializeScript, 3000);

```


## TapSwap

```bash
let GAME_SETTINGS = {
    minClickDelay: 30,
    maxClickDelay: 50,
    pauseMinTime: 100000,
    pauseMaxTime: 300000,
    energyThreshold: 25
};

let isGamePaused = false;

const logPrefix = '%c[TapSwapBot By DefiWimar] ';

const originalLog = console.log;
console.log = function () {};

console.error = console.warn = console.info = console.debug = () => {};

function triggerEvent(element, eventType, properties) {
    const event = new MouseEvent(eventType, properties);
    element.dispatchEvent(event);
}

function getRandomCoordinateInCircle(radius) {
    let x, y;
    do {
        x = Math.random() * 2 - 1;
        y = Math.random() * 2 - 1;
    } while (x * x + y * y > 1);
    return {
        x: Math.round(x * radius),
        y: Math.round(y * radius)
    };
}

function getCurrentEnergy() {
    const energyElement = document.querySelector("._h4_1w1my_1");
    if (energyElement) {
        return parseInt(energyElement.textContent);
    }
    return null;
}

function checkCoinAndClick() {
    if (isGamePaused) {
        setTimeout(checkCoinAndClick, GAME_SETTINGS.minClickDelay);
        return;
    }

    const button = document.querySelector("#ex1-layer img");

    if (button) {
        clickButton(button);
    } else {
        setTimeout(checkCoinAndClick, GAME_SETTINGS.minClickDelay);
    }
}

function clickButton(button) {
    if (isGamePaused) {
        setTimeout(checkCoinAndClick, GAME_SETTINGS.minClickDelay);
        return;
    }

    const currentEnergy = getCurrentEnergy();
    if (currentEnergy !== null && currentEnergy < GAME_SETTINGS.energyThreshold) {
        const pauseTime = GAME_SETTINGS.pauseMinTime + Math.random() * (GAME_SETTINGS.pauseMaxTime - GAME_SETTINGS.pauseMinTime);
        setTimeout(checkCoinAndClick, pauseTime);
        return;
    }

    const rect = button.getBoundingClientRect();
    const radius = Math.min(rect.width, rect.height) / 2;
    const { x, y } = getRandomCoordinateInCircle(radius);

    const clientX = rect.left + radius + x;
    const clientY = rect.top + radius + y;

    const commonProperties = {
        bubbles: true,
        cancelable: true,
        view: window,
        clientX: clientX,
        clientY: clientY,
        screenX: clientX,
        screenY: clientY,
        pageX: clientX,
        pageY: clientY,
        pointerId: 1,
        pointerType: "touch",
        isPrimary: true,
        width: 1,
        height: 1,
        pressure: 0.5,
        button: 0,
        buttons: 1
    };

    triggerEvent(button, 'pointerdown', commonProperties);
    triggerEvent(button, 'mousedown', commonProperties);
    triggerEvent(button, 'pointerup', { ...commonProperties, pressure: 0 });
    triggerEvent(button, 'mouseup', commonProperties);
    triggerEvent(button, 'click', commonProperties);

    const delay = GAME_SETTINGS.minClickDelay + Math.random() * (GAME_SETTINGS.maxClickDelay - GAME_SETTINGS.minClickDelay);
    setTimeout(checkCoinAndClick, delay);
}

checkCoinAndClick();


```

## MeMEFi

```bash
(function() {
    let GAME_SETTINGS = {
        minClickDelay: 30,
        maxClickDelay: 130,
        pauseMinTime: 100000,
        pauseMaxTime: 300000,
        autoSpin: false,
    };

    let isGamePaused = false;

    function triggerClick(element) {
        const randomX = Math.floor(Math.random() * 422);
        const randomY = Math.floor(Math.random() * 321);

        const events = [
            new MouseEvent('pointerover', { view: window, bubbles: true, cancelable: true, clientX: randomX, clientY: randomY }),
            new MouseEvent('pointerenter', { view: window, bubbles: true, cancelable: true, clientX: randomX, clientY: randomY }),
            new MouseEvent('mouseover', { view: window, bubbles: true, cancelable: true, clientX: randomX, clientY: randomY }),
            new MouseEvent('mousedown', { view: window, bubbles: true, cancelable: true, clientX: randomX, clientY: randomY }),
            new MouseEvent('pointerdown', { view: window, bubbles: true, cancelable: true, clientX: randomX, clientY: randomY }),
            new MouseEvent('mouseup', { view: window, bubbles: true, cancelable: true, clientX: randomX, clientY: randomY }),
            new MouseEvent('pointerup', { view: window, bubbles: true, cancelable: true, clientX: randomX, clientY: randomY })
        ];

        events.forEach(event => element.dispatchEvent(event));
    }

    function findAndClick() {
        if (isGamePaused) {
            setTimeout(findAndClick, 1000);
            return;
        }

        const targetElement = document.querySelector('div[aria-disabled="false"].css-79elbk');

        if (targetElement) {
            function clickWithRandomInterval() {
                if (isGamePaused) {
                    setTimeout(findAndClick, 1000);
                    return;
                }
                triggerClick(targetElement);
                const randomInterval = Math.floor(Math.random() * (GAME_SETTINGS.maxClickDelay - GAME_SETTINGS.minClickDelay + 1)) + GAME_SETTINGS.minClickDelay;
                setTimeout(clickWithRandomInterval, randomInterval);

                if (Math.random() < 0.1) {
                    checkAndClickIconButton();
                }
            }
            clickWithRandomInterval();
        } else {
            if (attempts < 5) {
                attempts++;
                setTimeout(findAndClick, 3000);
            } else {
                attempts = 0;
                setTimeout(findAndClick, 3000);
            }
        }
    }

    function checkAndClickIconButton() {
        const spanElement = document.querySelector('.MuiTypography-root.MuiTypography-bodySmallExtraBoldV2.css-1sz6sja');
        
        if (spanElement && /^(Claim)$/.test(spanElement.textContent)) {
            const iconButton = document.querySelector('button.MuiButtonBase-root.MuiButton-root.MuiButton-primary.MuiButton-primaryPrimary.MuiButton-sizeLarge.MuiButton-primarySizeLarge.MuiButton-colorPrimary.css-y90z6f');
            if (iconButton) {
                iconButton.click();
                waitForClaimButton();
            }
        }
    }

    function waitForClaimButton() {
        const checkInterval = setInterval(() => {
            const claimButton = document.querySelector('body > div.MuiDrawer-root.MuiDrawer-modal.MuiModal-root.css-1muh5pq > div.MuiPaper-root.MuiPaper-elevation.MuiPaper-elevation16.MuiDrawer-paper.MuiDrawer-paperAnchorBottom.css-dsgero > div.MuiBox-root.css-4q3rnc > button');
            if (claimButton) {
                clearInterval(checkInterval);
                claimButton.click();
            }
        }, 1000);

        setTimeout(() => {
            clearInterval(checkInterval);
        }, 30000);
    }

    let attempts = 0;
    findAndClick();

    setInterval(() => {
        if (!document.querySelector('div[aria-disabled="false"].css-79elbk')) {
            attempts = 0;
            findAndClick();
        }
    }, 5000);
})();
```


# ▄︻デ𝙂𝙚𝙩 𝙇𝙖𝙩𝙚𝙨𝙩 𝘼𝙞𝙧𝙙𝙧𝙤𝙥𝙨 & 𝙐𝙥𝙙𝙖𝙩𝙚𝙨═━一

### ▄︻デ𝙅𝙤𝙞𝙣 𝙏𝙚𝙡𝙚𝙜𝙧𝙖𝙢═━一 [🎀  𝐻𝒾𝒹𝒹𝑒𝓃 𝒢𝑒𝓂  🎀](https://t.me/hiddengemnews) 

### ░▒▓█►─═  𝓗𝓲𝒹ᗪ𝓔η Ǥέ𝕄 ═─◄█▓▒░
