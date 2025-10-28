# DocuSeal React Components

[📙 Documentation](https://www.docuseal.com/docs/embedded/form#react) | [💻 Examples](https://github.com/docusealco/docuseal-react-examples) | [🚀 Demo App](https://embed.docuseal.tech/)

This package provides a convenient way to embed [DocuSeal](https://www.docuseal.com) into React apps. Sign documents and create document forms directly in your apps.

[Embedded Signing Form](#signing-form) 

![Signing Form](https://github.com/user-attachments/assets/5c92b842-7687-4341-88a1-64ac26c1e2e0)

[Embedded Form Builder](#form-builder) 

![Form Builder](https://github.com/user-attachments/assets/7645a4fb-7399-4cce-bb90-e077a8a1ce95)


## Installation

### Install from npm (Original Package)

```bash
npm install @docuseal/react
```

### Install from GitHub (This Forked Version - No JWT Required)

```bash
npm install github:DragonEmperor9480/docuseal-react
```

Or add to your `package.json`:

```json
{
  "dependencies": {
    "@docuseal/react": "github:DragonEmperor9480/docuseal-react"
  }
}
```

## Documentation

For detailed documentation, please click [here](https://www.docuseal.com/docs/embedded).

## Usage

### Signing Form

Copy public DocuSeal form URL from [https://docuseal.com](https://docuseal.com) and use it in the `src` component prop:

```jsx
import React from "react"
import { DocusealForm } from '@docuseal/react'

export function App() {
  return (
    <div className="app">
      <DocusealForm
        src="https://docuseal.com/d/LEVGR9rhZYf86M"
        email="signer@example.com"
      />
    </div>
  );
}
```

### Form Builder
#### React Client Render
```jsx
import React, { useEffect, useState } from 'react'
import { DocusealBuilder } from '@docuseal/react'

export function App() {
  const [token, setToken] = useState()

  useEffect(() => {
    fetch('/api/docuseal/builder_token', {
      method: 'POST'
    }).then(async (resp) => {
      const data = await resp.json()

      setToken(data.token)
    })
  }, []);

  return (
    <div className="app">
      {token && <DocusealBuilder token={token} />}
    </div>
  );
}
```

To protect the template builder from unathorized access a secure token (JWT) should be generated on the back-end:

```js
const express = require('express');
const jwt = require('jsonwebtoken');

const app = express();

app.post('/api/docuseal/builder_token', (req, res) => {
  const token = jwt.sign({
    user_email: 'your-docuseal-user-email@company.com',
    integration_email: 'customer@example.com', // replace with current user email
    name: 'Integration W-9 Test Form',
    document_urls: ['https://www.irs.gov/pub/irs-pdf/fw9.pdf'],
  }, process.env.DOCUSEAL_TOKEN);

  res.json({ token });
});

app.listen(8080, () => {
  console.log(`Server is running`);
});
```

Obtain secret API token (`DOCUSEAL_TOKEN` env variable) to sign JWT from [https://console.docuseal.com/api](https://console.docuseal.com/api).

Find Express.js example project [here](https://github.com/docusealco/docuseal-react-examples/tree/master/expess-app).

#### Next.js SSR
```js
import jwt from 'jsonwebtoken';
import { DocusealBuilder } from '@docuseal/react'

export default function Home() {
  const token = jwt.sign( {
    user_email: process.env.DOCUSEAL_USER_EMAIL,
    integration_email: 'test@example.com',
    name: 'Integration W-9 Test Form',
    document_urls: ['https://www.irs.gov/pub/irs-pdf/fw9.pdf'],
  }, process.env.DOCUSEAL_TOKEN);

  return (
    <div>
      <h1>Docuseal Builder</h1>
      <DocusealBuilder token={token} />
    </div>
  );
}
```
Find Next.js example project [here](https://github.com/docusealco/docuseal-react-examples/tree/master/next-app).

# License

MIT

## Usage Without JWT Authentication( my custom changes)

If your users are already authenticated in your application, you can use the DocuSeal components without requiring a JWT token.

### Form Builder (Without Token)

```jsx
import React from 'react'
import { DocusealBuilder } from '@docuseal/react'

export function App() {
  return (
    <div className="app">
      <DocusealBuilder 
        name="My Document Form"
        roles={['Signer', 'Approver']}
        // No token required!
      />
    </div>
  );
}
```

### Form Builder (With Optional Token)

The `token` prop is now optional. You can still use it when needed:

```jsx
import React from 'react'
import { DocusealBuilder } from '@docuseal/react'

export function App() {
  const token = getTokenIfNeeded(); // Optional

  return (
    <div className="app">
      <DocusealBuilder 
        token={token}  // Optional prop
        name="My Document Form"
      />
    </div>
  );
}
```

**Note:** When using without authentication, ensure your DocuSeal instance is configured appropriately for your security requirements.
