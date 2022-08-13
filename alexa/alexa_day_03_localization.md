# Localization

## Getting ready

Now that the skill is running again as it is supposed to do, we start again with the localization.

Following again
an [example](https://github.com/xavidop/alexa-typescript-lambda-helloworld/blob/master/lambda/custom/src/interceptors/LocalizationRequestInterceptor.ts)
found before, we will use [i18next](https://www.i18next.com/) to configure internationalization.

The summary of the steps is:

* run `npm install i18next i18next-sprintf-postprocessor`
* run `npm i --save-dev @types/i18next-sprintf-postprocessor`
* create a _**LocalizationRequestInterceptor**_ object and register it as .
* create a file **_constants.ts_** that contains the enumerations `LocaleType`, and `Strings`
* create a file **_strings.ts_** that defines an interface `IStrings` that marks all the messages that requires
  translation
* create an object `strings` with the implementation of the interface for each supported locale
    ```typescript
    export const strings = {
      [LocaleTypes.enUS]: {
        translation: {
          SKILL_NAME: 'Hello world',
          WELCOME_MSG: 'This is the welcome message',
          HELLO_MSG: 'Hello message',
          HELP_MSG: 'Help message',
          GOODBYE_MSG: 'Goodbye message',
          REFLECTOR_MSG: 'Reflector message {{intentName}}',
          FALLBACK_MSG: 'Fallback message',
          ERROR_MSG: 'Error message',
        } as IStrings,
      }
    };
    ```
* Update our handlers to retrieve the translated string (example)
    ```javascript
  const speakOutput = i18n.t(Strings.WELCOME_MSG);
  ```

> Not strictly related to translations, but for readability we introduce some helper functions as suggested in the
> articles read so far.

## Customize our messages

The code so far is based on the HelloWorld example, but our real use case is a bit more complex.

It has to offer a more complex set of options and should use the appropriate speech in each locale.

Unfortunately our invocation utterances are hardcoded in a .json file, we should look for a better way to manage them
programmatically inside our code, so we can treat them as parameters to our localized strings.

Going back to the very
first [page](https://developer.amazon.com/en-US/blogs/alexa/alexa-skills-kit/2020/10/code-first-alexa-skill-development-with-vs-code-and-the-ask-sdk-controls-framework.html)
we used to start the skill, we are follow the approach suggested there and use the `ControlInteractionModelGenerator`
available in the more recent `ask-sdk-controls` (we bumped up the version defined by the hello world template)

### Preparing the model generation

* Run `npm install ask-sdk-controls` to get the missing dependency.

* Create a file `src/models/ModelsGenerator.ts` that is going to contain the logic to generate our interaction model.
    ```typescript
    import {ControlInteractionModelGenerator} from "ask-sdk-controls"

    new ControlInteractionModelGenerator()
       .withInvocationName('hello world')
       .addIntent({ name: 'AMAZON.StopIntent' })
       .addIntent({ name: 'AMAZON.NavigateHomeIntent' })
       .addIntent({ name: 'AMAZON.HelpIntent' })
       .addIntent({ name: 'AMAZON.CancelIntent' })
       // Add a custom intent
       .addIntent({ name: 'HelloIntent', samples: [
           "hello",
           "how are you",
           "say hi world",
           "say hi",
           "hi",
           "say hello world",
           "say hello"
       ]})
       
       // Build and write (be careful, this overwrites your existing model!!!)
       .buildAndWrite('../skill-package/interactionModels/custom/en-US.json');
    ```

* Update the `package.json` to include the new script
    ```json
    {
      "prebuildmodel": "npm run compile",
      "buildmodel": "node dist/model/ModelGenerator.js"
    }
    ```

  > The new script can be executed only after a `compile` task since it will be loaded from the `dist` folder

* run `npm run compile && npm run buildmodel`. When the process finishes you should see some pending changes on
  the `en-US.json` file

## Localize the model

Following the example of the internationalized messages we create some structure that will help us define what we need.

Each language will have its own invocation name. In addition to the invocation name some intents will require samples to
trigger the intent.

Our IModel looks like this:

```typescript
interface IModel {
    invocationName: string;
    intents: { [intent in IntentTypes]?: string[] }
}
```

and we define a type `LocalizedModel` as follows:

```typescript
type LocalizedModel = { [key in LocaleTypes]: IModel }
```

Our final generator should look like this:

```typescript
import {IntentTypes, LocaleTypes} from "../common/constants";

interface IModel {
    invocationName: string;
    intents: { [intent in IntentTypes]?: string[] }
}

type LocalizedModel = { [key in LocaleTypes]: IModel }

export const models: LocalizedModel = {
    [LocaleTypes.enUS]: {
        invocationName: "theaters around me",
        intents: {
            [IntentTypes.ListTheaters]: [
                "list theaters",
                "which theaters",
                "what theaters"
            ]
        }
    },
    [LocaleTypes.itIT]: {
        invocationName: "cinema intorno a me",
        intents: {
            [IntentTypes.ListTheaters]: [
                "elenca cinema",
                "quali cinema",
                "quale cinema"
            ]
        }
    }
}
```

To avoid forgetting to run the compilation before building the model we define it as a dependency, updating our script
section to include the following script:

```json lines
{
  "prebuildmodel": "npm run compile"
}
```