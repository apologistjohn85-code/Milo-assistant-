# Milo-assistant-
// ===============================
// MILO - Personal Assistant
// ===============================

const Milo = {
    name: "Milo",
    userTitle: "boss",

    respond(message) {
        const text = message.toLowerCase().trim();

        // Greetings
        if (
            text.includes("hello") ||
            text.includes("hi") ||
            text.includes("hey")
        ) {
            return "Hello, boss. Milo is online and ready.";
        }

        // Identity
        if (
            text.includes("who are you") ||
            text.includes("what are you")
        ) {
            return "I'm Milo, your personal assistant.";
        }

        // Time
        if (text.includes("what time") || text === "time") {
            return `It's ${new Date().toLocaleTimeString([], {
                hour: "numeric",
                minute: "2-digit"
            })}, boss.`;
        }

        // Date
        if (
            text.includes("what date") ||
            text.includes("today's date")
        ) {
            return `Today is ${new Date().toLocaleDateString([], {
                weekday: "long",
                month: "long",
                day: "numeric"
            })}.`;
        }

        // Calculator
        if (text.startsWith("calculate ")) {
            const expression = message.substring(10).trim();

            try {
                // Basic calculator only
                if (!/^[0-9+\-*/().%\s]+$/.test(expression)) {
                    return "I can only calculate basic mathematical expressions.";
                }

                const answer = Function(
                    `"use strict"; return (${expression})`
                )();

                return `The answer is ${answer}, boss.`;
            } catch {
                return "I couldn't calculate that.";
            }
        }

        // Help
        if (text === "help" || text.includes("what can you do")) {
            return `
I can currently:
• Respond to basic commands
• Tell you the time
• Tell you the date
• Perform basic calculations
• Speak my responses
• Listen for voice commands

My AI model and additional tools can be connected later.
            `.trim();
        }

        // Unknown command
        return `I heard you, boss. You said: "${message}". My advanced AI brain isn't connected yet.`;
    }
};


// ===============================
// CHAT SYSTEM
// ===============================

function askMilo() {
    const input = document.getElementById("command");
    const response = document.getElementById("response");

    const message = input.value.trim();

    if (!message) {
        response.textContent = "I'm listening, boss.";
        speak(response.textContent);
        return;
    }

    const answer = Milo.respond(message);

    response.textContent = answer;

    speak(answer);

    input.value = "";
}


// ===============================
// VOICE OUTPUT
// ===============================

function speak(text) {
    if (!("speechSynthesis" in window)) {
        return;
    }

    window.speechSynthesis.cancel();

    const speech = new SpeechSynthesisUtterance(text);

    speech.rate = 0.95;
    speech.pitch = 1.0;

    window.speechSynthesis.speak(speech);
}


// ===============================
// VOICE INPUT
// ===============================

function startListening() {

    const SpeechRecognition =
        window.SpeechRecognition ||
        window.webkitSpeechRecognition;

    if (!SpeechRecognition) {
        alert("Voice recognition isn't supported by this browser.");
        return;
    }

    const recognition = new SpeechRecognition();

    recognition.lang = "en-US";
    recognition.interimResults = false;

    recognition.onstart = function () {
        console.log("Milo is listening...");
    };

    recognition.onresult = function (event) {

        const message =
            event.results[0][0].transcript;

        document.getElementById("command").value = message;

        askMilo();
    };

    recognition.onerror = function (event) {
        console.log("Voice error:", event.error);
    };

    recognition.start();
}


// ===============================
// ENTER KEY SUPPORT
// ===============================

document.addEventListener("DOMContentLoaded", () => {

    const input =
        document.getElementById("command");

    if (input) {
        input.addEventListener("keydown", (event) => {

            if (event.key === "Enter") {
                askMilo();
            }
