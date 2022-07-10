# Customization

After doing some initial setup and tests, we can move forward with more complex stuff.

## Trying on Alexa

After testing the skill on the Alexa development console, I wanted to take the skill for a spin on my actual Alexa.

Unfortunately I cannot get it to work.... and I should have known before trying already, my Alexa is not configured on the english locale and that's all we set up so far.

The easy solution might be to change the locale to english, but we are here to learn so let's try to add a new locale too.

## Introducing a new locale

We could go to the [documentation](https://developer.amazon.com/en-US/docs/alexa/custom-skills/develop-skills-in-multiple-languages.html) and read some details about multi-language implementations, but considering how it has been simple so far we are going to try to copy the `en-US.json` and rename it to a different locale to update.

Doing a search in all files we see that _en-US_ is also specified in the `skill.json`, so we update that too, and we redeploy the skill.

**Alexa now reacts!**

## Customizing the answers

Now that we have introduced also another locale is time to take a look at the responses defined in our intent.

At the moment in whatever language we interact with the skill the answer is the english text that is defined in the intent handler, and we need to modify it to answer in the more appropriate language.

> The Visual Code IDE in the current setup does not seem to support suggestions to fix errors, so we are going to pause a bit the customization of the answer and try to make our IDE more reactive migrating to [typescript](https://xavidop.me/alexa/2020-07-20-alexa-typescript/#estructura-del-proyecto)

## Migrating to TypeScript

In our project we run the following command:
`npm install --save-dev @types/node typescript ts-node cpy-cli rimraf`

We set up TypeScript with the following `tsconfig.json` at the same level of the `package.json`

```json
{
    "compilerOptions": {
      "module": "commonjs",
      "esModuleInterop": true,
      "target": "es6",
      "moduleResolution": "node",
      "sourceMap": true,
      "outDir": "build"
    },
    "lib": ["es2015"],
    "include": [
        "src/**/*.ts"
    ]
  }
```

We create a folder **src**, and we migrate all the code that is in `index.js` in that folder in properly separated files.

To begin we are going to create:
* `src/index.ts`: In this file we keep the registration of the handler.
* `src/intents/Default.ts`: In this file we include all the handlers that we have not touched so far.
* `src/intents/ListTheaters.ts`: In this file we include the intent we are working on.

We update the `package.json` file to include the following scripts
```json
{
  "scripts": {
    "clean": "rimraf build",
    "compile": "tsc --build tsconfig.json --pretty",
    "test": "echo \"Error: no test specified\" && exit 0",
    "build-final": "cpy package.json build && cd build/ && npm install --production",
    "build": "npm run clean && npm run test && npm run compile && npm run build-final"
  }
}
```

It's time to delete the `index.js`, and instruct the skill that the new index.js is now in `lambda/build`.

We do this by editing the `skill.json` file specifying in the **endpoint** node the attribute `"sourceDir":"lambda/build""`

We do a quick check running `npm run build`, all seems good.

Let's commit, push, and see what happens....


**The deployment broke**

## Broken deployment

It seems that the tutorial I was getting inspiration from builds with the ASK Cli while I am using the Visual Studio extension.

Reaching out to the Alexa community proved helpful. There's a good [article](https://arctouch.com/blog/alexa-skill-development-best-practices/) that can be followed.

After adapting our configuration and pushing also the dist/ folder our skill is working again.

