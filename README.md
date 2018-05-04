# Puppeteer Lambda

Execute puppeteer v1.3.0 scripts within the AWS Lambda environment.

## Usage

```js
const puppeteer = require('puppeteer')
const { extract, cleanup } = require('puppeteer-lambda')

(async () => {
  // Extract the headless chrome executable and return its path
  // If a previous Lambda invocation has extracted the executable, it will be reused
  const executablePath = await extract()
  
  // Initialize a new browser instance with puppeteer to execute within Lambda
  const browser = await puppeteer.launch({
    ignoreHTTPSErrors: true,
    args: [
      '--disable-dev-shm-usage',
      '--disable-gpu',
      '--single-process',
      '--no-zygote',
      '--no-sandbox'
    ],
    executablePath
  })
  
  // Run puppeteer script
  const page = await browser.newPage()
  await page.goto('https://example.com')
  await page.screenshot({path: 'example.png'})
  await browser.close()
  
  // Cleanup the TMP folder after each execution otherwise Chromium's
  // garbage will cause the Lambda container to run out of space
  await cleanup()
})()
```

## Test

```sh
$ npm test
```