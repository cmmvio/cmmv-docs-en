# Email

The `@cmmv/email` module provides a unified interface for sending emails in CMMV-based applications, using [nodemailer](https://nodemailer.com/) for email transport and optional AWS SES integration.

## Features

* **SMTP & AWS SES Support:** Send emails via standard SMTP or Amazon SES.

* **Seamless Integration:** Works with the CMMV ecosystem.

* **Transport Options:** Customizable transport options via configuration.

* **Logging Support:** Toggle email logging for debugging purposes.

## Installation

Install the `@cmmv/email` package via npm:

```bash
$ pnpm add @cmmv/email nodemailer aws-sdk
```

## Configuration

The `@cmmv/email` module requires configuration settings for SMTP or AWS SES integration. These can be set in the `.cmmv.config.cjs` file:

```javascript
module.exports = {
    email: {
        host: process.env.EMAIL_HOST || "smtp.mailtrap.io",
        port: process.env.EMAIL_PORT || 587,
        secure: false,  // Set to true for SSL
        auth: {
            user: process.env.EMAIL_USER,
            pass: process.env.EMAIL_PASS,
        },
        logger: true,  // Enable logging
        debug: true,   // Enable debug mode
        SES: {
            region: process.env.AWS_REGION,
            accessKeyId: process.env.AWS_ACCESS_KEY_ID,
            secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY
        }
    }
};
```

## Setting Up the Application

In your ``index.ts``, include the ``EmailModule`` in the application:

```typescript
import { Application } from "@cmmv/core";
import { DefaultAdapter, DefaultHTTPModule } from "@cmmv/http";
import { EmailModule } from "@cmmv/email";

Application.create({
    httpAdapter: DefaultAdapter,
    modules: [
        DefaultHTTPModule,
        EmailModule,
    ],
});
```

## Using the EmailService

The `EmailService` can be integrated with the `@cmmv/queue` module for asynchronous email processing.

```typescript
import { Channel, Consume, QueueMessage } from "@cmmv/queue";
import { EmailService } from "@cmmv/email";

@Channel("email-queue")
export class EmailConsumer {
    constructor(private readonly emailService: EmailService) {}

    @Consume("send-email")
    public async onSendEmail(@QueueMessage() message) {
        await this.emailService.send(
            message.from,
            message.to,
            message.subject,
            message.html,
            message.options
        );
        console.log("Email sent successfully");
    }
}
```

## AWS SES Integration

If AWS SES settings are provided in the configuration, the module will automatically use AWS SES as the email provider.

```javascript
SES: {
    region: "us-east-1",
    accessKeyId: "YOUR_AWS_ACCESS_KEY",
    secretAccessKey: "YOUR_AWS_SECRET_KEY"
}
```
