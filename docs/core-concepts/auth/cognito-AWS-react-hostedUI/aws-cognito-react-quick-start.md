# Add the example code to your application

Configure your user pool app client with allowed callback URLs, logout URLs, and the scopes that you want to request, for example *openid* and *profile*. [Learn more](https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-settings-client-apps.html)





Install the [oidc-client-ts](https://github.com/authts/oidc-client-ts)  and [react-oidc-context](https://github.com/authts/react-oidc-context)  libraries.

```bash
npm install oidc-client-ts react-oidc-context --save
```

Configure *react-oidc-context* with the OIDC properties of your user pool.

```jsx
// index.js
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import { AuthProvider } from "react-oidc-context";

const cognitoAuthConfig = {
  authority: "https://cognito-idp.eu-central-1.amazonaws.com/eu-central-1_jcmV",
  client_id: "7kkicfos0nc8",
  redirect_uri: "http://localhost:5173/",
  response_type: "code",
  scope: "email openid phone",
};

const root = ReactDOM.createRoot(document.getElementById("root"));

// wrap the application with AuthProvider
root.render(
  <React.StrictMode>
    <AuthProvider {...cognitoAuthConfig}>
      <App />
    </AuthProvider>
  </React.StrictMode>
);
```



Generate a sign-in button that initiates an authorization request with your user pool OIDC provider, and a sign-out button that initiates a logout request.

```jsx
// App.js

import { useAuth } from "react-oidc-context";

function App() {
  const auth = useAuth();

  const signOutRedirect = () => {
    const clientId = "7kkicfmss0nc8";
    const logoutUri = "<logout uri>";
    const cognitoDomain = "https://eu-central-1qrv.auth.eu-central-1.amazoncognito.com";
    window.location.href = `${cognitoDomain}/logout?client_id=${clientId}&logout_uri=${encodeURIComponent(logoutUri)}`;
  };

  if (auth.isLoading) {
    return <div>Loading...</div>;
  }

  if (auth.error) {
    return <div>Encountering error... {auth.error.message}</div>;
  }

  if (auth.isAuthenticated) {
    return (
      <div>
        <pre> Hello: {auth.user?.profile.email} </pre>
        <pre> ID Token: {auth.user?.id_token} </pre>
        <pre> Access Token: {auth.user?.access_token} </pre>
        <pre> Refresh Token: {auth.user?.refresh_token} </pre>

        <button onClick={() => auth.removeUser()}>Sign out</button>
      </div>
    );
  }

  return (
    <div>
      <button onClick={() => auth.signinRedirect()}>Sign in</button>
      <button onClick={() => signOutRedirect()}>Sign out</button>
    </div>
  );
}

export default App;
```
