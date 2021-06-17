+++
author = "Daryl Walleck"
title = "Automatically Create Playwright Traces with Jest"
date = "2021-06-17"
description = ""
tags = [
    "Node",
    "Playwright",
    "Jest"
]
codeMaxLines = 50
+++

In the Playwright 1.12 release, one of the new features added was the ability to
create traces of test runs. I find traces to be more helpful than videos because
they allow me to step through my test steps at my own pace. Unlike videos, there
isn't currently a way to configure Playwright to record traces by default. However,
if you use Jest's circus test runner, you can modify the test lifecycle
using Custom Environments. To enable the circus runner, first run `npm install --save-dev jest-circus`, and then add the following line to your `jest.config.js` file:

```javascript
module.exports = {
    // All of the rest of your config
    testEnvironment: "./customEnvironment.js",
}
```

With the circus runner enabled, we then need a CustomEnvironment that injects
Playwright traces into the test lifecycle. Create a file for the new environment (I named mine `customEnvironment.js`) with this content:

```javascript
const PlaywrightEnvironment = require("jest-playwright-preset/lib/PlaywrightEnvironment")
    .default

class CustomEnvironment extends PlaywrightEnvironment {
    constructor(config, context) {
        super(config, context)
    }
    async setup() {
        await super.setup()
        const { context } = this.global;
        await context.tracing.start({ screenshots: true, snapshots: true });
    }

    async teardown() {
        await super.teardown()
    }

    async handleTestEvent(event) {

        if (event.name === "test_done") {
            const testPassed = event.test.errors.length === 0 ? true : false
            const testName = event.test.name.replace(/ /g, "_")
            const { context } = this.global;
            const { browserName } = this._config
            await context.tracing.stop({ path: `traces/${testName}-${browserName}.zip` });
        }
    }
}

module.exports = CustomEnvironment
```

With this custom environment, I’m recording a trace for every test that is run. However, this could be modified to store traces on test failures, if an environment variable was set, or keep all traces by amending the trace name with the current DateTime.

Lastly, we need to amend our `jest.config.js` file to use our custom environment:

```javascript
module.exports = {
    // All of the rest of your config
    testEnvironment: "./customEnvironment.js",
}
```

And with that change, you're done! All of your traces will be stored in the `/traces`
directory of the project. Given that the Playwright team already allows videos to be recorded automatically, they may add a similar feature for traces. Until
then, I hope this method helps!
