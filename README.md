# sean-training-program

Sean's 12-week Middle Blocker training tracker. Live at https://sanishj.github.io/sean-training-program/

## Run it on your computer
```
npm run serve
```
Then open http://localhost:5500. Local runs use a separate test record (`sean-dev`) and show a red **TEST DATA** badge, so nothing you click touches Sean's real history.

## Publish a change
```
git add -A
git commit -m "Describe the change"
git push
```
GitHub Pages updates the live site in about a minute.

## Change who can sign in
Edit the emails in both `index.html` (`OWNER_EMAIL`, `VIEWER_EMAILS`) and `firestore.rules`, push, then:
```
npm run deploy:rules
```
