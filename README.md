# Blum-click-android
Blum
// ==UserScript==
// @name         !BlumFarm!
// @version      2.6
// @namespace    Jalol
// @author       Jalol
// @updateURL 
// @match        https://telegram.blum.codes/*
// @grant        none
// @icon         https://raw.githubusercontent.com/ilfae/ilfae/main/logo.webp
// ==/UserScript==

let GAME_SETTINGS = {
    clickPercentage: {
        bomb: 0,
        ice: 30,
        flower: Math.floor(Math.random() * (90 - 80 + 1)) + 80,
        dogs: Math.floor(Math.random() * (90 - 80 + 1)) + 80,
    },
    autoClickPlay: false,
};

let isGamePaused = true;
let isSettingsOpen = false;

try {
    let gameStats = {
        isGameOver: false,
    };

    const originalPush = Array.prototype.push;
    Array.prototype.push = function (...items) {
        if (!isGamePaused) {
            items.forEach(item => handleGameElement(item));
        }
        return originalPush.apply(this, items);
    };

    async function handleGameElement(element) {
        if (!element || !element.asset) return;

        const { assetType } = element.asset;
        const randomValue = Math.random() * 100;

        switch (assetType) {
            case "CLOVER":
                if (randomValue < GAME_SETTINGS.clickPercentage.flower) {
                    await clickElementWithDelay(element);
                }
                break;
            case "BOMB":
                if (randomValue < GAME_SETTINGS.clickPercentage.bomb) {
                    await clickElementWithDelay(element);
                }
                break;
            case "FREEZE":
                if (randomValue < GAME_SETTINGS.clickPercentage.ice) {
                    await clickElementWithDelay(element);
                }
                break;
            case "DOGS":
                if (randomValue < GAME_SETTINGS.clickPercentage.dogs) {
                    await clickElementWithDelay(element);
                }
                break;
            default:
                console.log(`Unknown element type: ${assetType}`);
        }
    }

    function clickElementWithDelay(element) {
        const clickDelay = Math.floor(Math.random() * (1500 - 200 + 1)) + 200;
        setTimeout(() => {
            element.onClick(element);
            element.isExplosion = true;
            element.addedAt = performance.now();
        }, clickDelay);
    }

    function checkGameCompletion() {
        const rewardElement = document.querySelector('#app > div > div > div.content > div.reward');
        if (rewardElement && !gameStats.isGameOver) {
            gameStats.isGameOver = true;
        }
    }

    function getNewGameDelay() {
        return Math.floor(Math.random() * (3000 - 1000 + 1)) + 1000;
    }

    function checkAndClickPlayButton() {
        const playButtons = document.querySelectorAll('button.kit-button.is-large.is-primary, a.play-btn[href="/game"], button.kit-button.is-large.is-primary');

        playButtons.forEach(button => {
            if (!isGamePaused && GAME_SETTINGS.autoClickPlay && (/Play/.test(button.textContent) || /Continue/.test(button.textContent))) {
                setTimeout(() => {
                    button.click();
                    gameStats.isGameOver = false;
                }, getNewGameDelay());
            }
        });
    }

    function continuousPlayButtonCheck() {
        checkAndClickPlayButton();
        setTimeout(continuousPlayButtonCheck, 1000);
    }

    const observer = new MutationObserver(mutations => {
        for (const mutation of mutations) {
            if (mutation.type === 'childList') {
                checkGameCompletion();
            }
        }
    });

    const appElement = document.querySelector('#app');
    if (appElement) {
        observer.observe(appElement, { childList: true, subtree: true });
    }

    const floatingWindow = document.createElement('div');
    floatingWindow.style.position = 'fixed';
    floatingWindow.style.top = '0';
    floatingWindow.style.left = '50%';
    floatingWindow.style.transform = 'translateX(-50%)';
    floatingWindow.style.zIndex = '9999';
    floatingWindow.style.backgroundColor = 'Black';
    floatingWindow.style.padding = '10px 20px';
    floatingWindow.style.borderRadius = '10px';
    document.body.appendChild(floatingWindow);
    
// Suzuvchi oynani harakatlantirish funksiyalari
let isDragging = false;
let offsetX, offsetY;

floatingWindow.addEventListener('touchstart', (e) => {
    isDragging = true;
    const touch = e.touches[0];
    offsetX = touch.clientX - floatingWindow.getBoundingClientRect().left;
    offsetY = touch.clientY - floatingWindow.getBoundingClientRect().top;
});

floatingWindow.addEventListener('touchmove', (e) => {
    if (isDragging) {
        const touch = e.touches[0];
        floatingWindow.style.left = `${touch.clientX - offsetX}px`;
        floatingWindow.style.top = `${touch.clientY - offsetY}px`;
    }
});

floatingWindow.addEventListener('touchend', () => {
    isDragging = false;
});

    
    const OutGamePausedTrue = document.createElement('a');
    OutGamePausedTrue.href = 'https://t.me/Eral1yevvv';
    OutGamePausedTrue.textContent = 'Jalol👿';
    OutGamePausedTrue.style.color = 'white';
    floatingWindow.appendChild(OutGamePausedTrue);

    const buttonsContainer = document.createElement('div');
    buttonsContainer.style.display = 'flex';
    buttonsContainer.style.justifyContent = 'center';
    floatingWindow.appendChild(buttonsContainer);

    const pauseButton = document.createElement('button');
    pauseButton.textContent = '▷';
    pauseButton.style.padding = '4px 8px';
    pauseButton.style.backgroundColor = '#5d2a8f';
    pauseButton.style.color = 'white';
    pauseButton.style.border = 'none';
    pauseButton.style.borderRadius = '10px';
    pauseButton.style.cursor = 'pointer';
    pauseButton.style.marginRight = '5px';
    pauseButton.onclick = () => {
        toggleGamePause();
    };
    floatingWindow.appendChild(pauseButton);

    const settingsButton = document.createElement('button');
    settingsButton.textContent = '⚙';
    settingsButton.style.padding = '4px 8px';
    settingsButton.style.backgroundColor = '#5d2a8f';
    settingsButton.style.color = 'white';
    settingsButton.style.border = 'none';
    settingsButton.style.borderRadius = '10px';
    settingsButton.style.cursor = 'pointer';
    settingsButton.onclick = toggleSettings;
    floatingWindow.appendChild(settingsButton);

    const settingsContainer = document.createElement('div');
    settingsContainer.style.display = 'none';
    settingsContainer.style.marginTop = '10px';
    floatingWindow.appendChild(settingsContainer);

    function toggleSettings() {
        isSettingsOpen = !isSettingsOpen;
        if (isSettingsOpen) {
            settingsContainer.style.display = 'block';
            settingsContainer.innerHTML = '';

            const table = document.createElement('table');
            table.style.color = 'white';

            const items = [
                { label: '☣︎ %', settingName: 'bomb' },
                { label: '☃︎ %', settingName: 'ice' },
                { label: '₿ %', settingName: 'flower' },
                { label: '𓃡 %', settingName: 'dogs' }
            ];

            items.forEach(item => {
                const row = table.insertRow();

                const labelCell = row.insertCell();
                labelCell.textContent = item.label;

                const inputCell = row.insertCell();
                const inputElement = document.createElement('input');
                inputElement.type = 'number';
                inputElement.value = GAME_SETTINGS.clickPercentage[item.settingName];
                inputElement.min = 0;
                inputElement.max = 100;
                inputElement.style.width = '50px';
                inputElement.addEventListener('input', () => {
                    GAME_SETTINGS.clickPercentage[item.settingName] = parseInt(inputElement.value, 10);
                });
                inputCell.appendChild(inputElement);
            });

            settingsContainer.appendChild(table);
        } else {
            settingsContainer.style.display = 'none';
        }
    }

    function toggleGamePause() {
        isGamePaused = !isGamePaused;
        if (isGamePaused) {
            pauseButton.textContent = '▷';
            GAME_SETTINGS.autoClickPlay = false;
        } else {
            pauseButton.textContent = '𓊕';
            GAME_SETTINGS.autoClickPlay = true;
            continuousPlayButtonCheck();
        }
    }

} catch (e) {
    console.error("!BlumFarm! error:", e);
}

// ==/UserScript==
