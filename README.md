# G.Milk order server

A small server (no packages to install, Node 18+) that makes the Easypaisa / JazzCash flow work for real:

1. A customer pays, uploads the screenshot and places an order on the website.
2. The server emails the shop owner the order and the screenshot, with **Confirm** and **Reject** buttons.
3. The owner checks the payment, taps Confirm (or Reject) and presses one more button on the page that opens.
4. The customer's **My Orders** page changes from Pending to Confirmed (or Rejected) within about 30 seconds.

It was tested end to end against a mock mail server (order, email with attachment, confirm, reject, wrong or tampered links, rate limit). It has **not** been tested against Gmail itself.

## 1. Get a Gmail App Password

1. Open your Google Account → Security, and turn on 2-Step Verification.
2. Search for "App passwords", create one named "G.Milk", and copy the 16-character password.

## 2. Deploy

Any host that runs Node works (Render, Railway, Fly.io, a VPS). On Render, for example:

- New → Web Service → connect a repository containing this `server` folder (set "Root Directory" to `server`).
- Build command: leave empty. Start command: `node index.js`.
- Add the settings from `.env.example` under Environment. `ADMIN_SECRET` must be a long random string, and `PUBLIC_URL` is the address Render gives you.

**Orders are stored in a file on the server.** Free hosting plans often wipe files when the service restarts, so use a persistent disk (set `DATA_DIR` to its path) or move to a database before taking real orders. The owner's email also keeps a copy of every order.

## 3. Connect the website

In `src/data/content.js` set:

```js
export const ORDER_API = { url: 'https://your-server.onrender.com' };
```

Then rebuild the website. The page must be served from a normal web address (for example the hosted site, or the single `g-milk.html` file); the pages published inside Claude block calls to other servers.

## Notes

- Prices in the cart come from the website and are not checked by the server. The owner should compare the amount in the email with the payment.
- The Confirm and Reject links in the email work for anyone who has the email, so keep it private.
- Rate limit: 8 orders per hour per IP address (`ORDERS_PER_HOUR`).
