# tdd-openai-workshop

A workshop for getting to know openai

## Step 1 - installing and configuration

Install everything with `npm install` and `npm install -g ts-node`
Run the test runner via `npm run test`

The test runner looks in the `tsconfig.include` directories for any files ending with `_test.ts`. Inside each file found, any functions exported with a name beginning with `test` will be run. Be sure to set `tsconfig.include` to point to your source directory.

Your files should go in src/

## Part 2 - Installing open-ai specific things

To use OpenAI's API, we must install the package:

```sh
npm install --save openai
```

We will use an env file for storing our API key from openai. To read the env file in our code, we'll use dotenv. Install it now.

Create an env file for the key:

```
OPENAI_API_KEY=
```

Install dotenv:

```sh
npm install --save dotenv
```

Usage:

```typescript
import * as dotenv from "dotenv";
dotenv.config();
```

To use API in our code, we should create an instance of the API. Create a new file called OpenAI.ts, and put the following content in it:

```typescript
import * as dotenv from "dotenv";
import { Configuration, OpenAIApi } from "openai";

dotenv.config();

const configuration = new Configuration({
  apiKey: process.env.OPENAI_API_KEY || "",
});

export const openai = new OpenAIApi(configuration);
```

## Step 4 - Parrot

Write a function that will return exactly the same string as the argument passed to it. Do this without openai - write it as you would any other TypeScript function.

```typescript
parrot("hello world") === "hello world";
```

Write a test for this function. Add some edge cases. It should look something like:

```typescript
import * as assert from "assert";
import { parrot } from ".";

export async function testParrot() {
  let prompt = "hello world";
  assert.deepStrictEqual(await parrot(prompt), prompt);
}
```

Now we would like to replicate this behaviour through OpenAI. When we provide input to GPT, the format is through several messages - either a "system" message, or a "user" message. The "system" prompt can be thought of as the instructions on how to intepret the user messages. In this case, we will tell GPT that it is a parrot and should repeat back given words to you. Getting the prompt right can take a lot of time and tweaking to get exaclty what you want.

```typescript
const prompt =
  "You are a parrot. Repeat back exactly what the user says and nothing more. If the input is empty, return nothing";
const completion = await openai.createChatCompletion({
  model: process.env.OPENAI_GPT_MODEL || "gpt-3.5-turbo",
  messages: [
    { role: "system", content: prompt },
    { role: "user", content: speech },
  ],
  temperature: 1,
  n: 1,
});
```

Note that your test may be failing - working with LLM models means accepting non-perfect responses. Adjust your tests to account for this.

## Step 5 - calculator

Now we will define a calculator that can do basic functions - addition, subtraction, multiplication and division.

Write a Typescript function that takes an input as a string, then returns the calculated result as a string.

```typescript
calc("1 + 2") === "3";
calc("5 - 3") === "2";
calc("3 * 2") === "6";
calc("1 / 2") === "0.5";
```

Write a test for this function.

Now write the OpenAI-powered version.
