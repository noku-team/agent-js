# How to Run Locally

Locally in your sdk repo, execute:

- `npm install`. To install all Node dependencies.
- `npm run webpack -- --watch` will start webpack in watch mode.
- In two separate terminals in a DFX project (create one if needed);
  - Start a `dfx replica`.
  - Start `dfx bootstrap --root $SDK_REPO_PATH/src/bootstrap/dist/ --providers http://localhost:8080 --port 8000`.
- Open your browser to `http://localhost:8000`. Change code, wait a few seconds for webpack to
build, reload browser.

If you need HTTPs (for example, using lvh or ic0.app using redirects), you will need to setup
your own nginx reverse proxy. Look up instructions online.

**Note that HTTPS is needed for the crypto API if you're accessing a non-localhost URL. This is
a limitation of the web API (see
https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts/features_restricted_to_secure_contexts[
the MDN documentation])**.



# Startup Process
- The bootstrap server determines which worker host it is using;
  - If there is a query param `workerHost`, use that value.
  - If there is a `dfinity-ic-host` value in local storage, uses that value.
  - If the host ends with `localhost` and contains more than 1 subdomain, use `dfinity.localhost`.
_This is used to test cross-domain worker._
  - If the host ends with `lvh.me`, use `dfinity.lvh.me`. _This is used to test cross-domain worker._
  - If the host ends with `ic0.app`, use `dfinity.ic0.app`.
  - Otherwise, don't use a worker (this is for localhost and development purposes).

- The bootstrap server determines the canister ID;
  - If there is a query param for `canisterId`, decode that value as text.
  - If there is a `dfinity-canister-id` value in local storage, uses that value.
  - If the host ends with `lvh.me`, split the host and use the first subdomain before
`ic0.app`. For example, `some-sub.01234567.lvh.me` would result in `01234567`.
_This is used to test cross-domain worker._
  - If the host ends with `ic0.app`, split the host and use the first subdomain before
`ic0.app`. For example, `some-sub.01234567.ic0.app` would result in `01234567`.
  - Otherwise, show a UI for the user to enter a canister ID.

- Create a worker with `${workerHost}/worker.js` using the same protocol.
- Get the canister's `/index.js` through the worker.
