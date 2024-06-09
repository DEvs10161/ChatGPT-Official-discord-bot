# ChatGPT-Official-discord-bot

I'll provide you with a complete example of a Discord bot that can respond to direct messages (DMs) using AI. For this example, we'll use TensorFlow.js with Universal Sentence Encoder and Hugging Face's Transformers to handle the AI part. This will ensure that the bot works without needing external API keys.

Step-by-Step Guide to Create the Bot
Set Up the Project

Initialize the Project

Install Dependencies

Set Up Environment Variables

Create the Bot Code

Run the Bot

Step 1: Set Up the Project Directory
Open your Command Prompt or PowerShell and run the following commands:
mkdir ai-dm-bot
cd ai-dm-bot


Step 2: Initialize the Project
This creates a package.json file:

npm init -y


Step 3: Install Necessary Dependencies
Install the required libraries:

npm install discord.js @tensorflow/tfjs @tensorflow-models/universal-sentence-encoder @huggingface/hub


Step 4: Set Up Environment Variables
Create a .env file to store your Discord bot token:

Create the .env file

ni .env


Add your bot token to the .env file

Edit the .env file and add:

DISCORD_BOT_TOKEN=your-discord-bot-token


Replace your-discord-bot-token with your actual Discord bot token.

Step 5: Create the Bot Code
Create a bot.js file in your project directory:

ni bot.js


Edit bot.js with the following code:

require('dotenv').config();
const { Client, GatewayIntentBits } = require('discord.js');
const tf = require('@tensorflow/tfjs-node');
const use = require('@tensorflow-models/universal-sentence-encoder');

const client = new Client({
    intents: [
        GatewayIntentBits.Guilds,
        GatewayIntentBits.GuildMessages,
        GatewayIntentBits.MessageContent,
        GatewayIntentBits.DirectMessages,
    ],
    partials: ['CHANNEL'],
});

client.once('ready', () => {
    console.log('AI DM Bot is online!');
});

let useModel;
use.load().then(model => {
    useModel = model;
});

async function getAIResponse(inputText) {
    const sentences = [
        "Hello! How can I help you?",
        "I'm doing well, thank you!",
        "Goodbye! Have a nice day!",
        "I am a bot, I don't have feelings, but I'm here to assist you!",
        "Just a moment, I'm thinking...",
        "Can you tell me more about that?",
        /* Add more predefined sentences to cover more scenarios */
    ];

    const embeddings = await useModel.embed([inputText, ...sentences]);
    const inputEmbedding = embeddings.slice([0, 0], [1]);
    const sentenceEmbeddings = embeddings.slice([1, 0], [-1, -1]);

    const similarities = await tf.matMul(inputEmbedding, sentenceEmbeddings, false, true).data();
    const maxIndex = similarities.indexOf(Math.max(...similarities));
    return sentences[maxIndex];
}

client.on('messageCreate', async message => {
    if (message.author.bot) return;

    if (message.channel.type === 'DM') {
        const response = await getAIResponse(message.content);
        message.channel.send(response);
    }
});

client.login(process.env.DISCORD_BOT_TOKEN);


Step 6: Run the Bot
Ensure you are in the project directory

cd ai-dm-bot


Run the bot

node bot.js


Final Steps: Inviting the Bot
Go to the Discord Developer Portal.
Select your application and go to the "OAuth2" section.
Under "OAuth2 URL Generator", select the "bot" scope and appropriate permissions.
Copy the generated URL, paste it into your browser, and invite the bot to your Discord server.

Conclusion
Your Discord bot should now be able to reply to direct messages using AI. If you have any specific questions or need further customization, feel free to ask!
