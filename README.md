# calc.bot

It’s a simple split-pane calculator that lets users enter expressions in human terms on the left-hand side and see live results on the right. 
For example, you can assign variables (`Rent is €2,000` , `Months = 12`) and perform calculations using them (`AnnualRent = Rent * Months`). If you feel updating one variable, the rest of the expressions update in real time.

## preview
Coming soon

### todos
- [ ] ability to restore most recent file on load using IndexedDB + added shortcuts
- [ ] preventing exit without saving changes
### finished
- [x] add toolbar with file, edit, view and help menus
- [x] allow user to set variables and words for operation
- [x] add modal
- [x] clearing on esc and disable spellcheck
- [x] favicon icons for modal
- [x] utilise mathjs / read [docs](https://mathjs.org/docs/index.html)
- [x] assign results to alphabetical variables + add copy button
- [x] add eur/% formatting, allow vars to be set on right of expression
- [x] add ability to open and save .calc files using local file system

## setup project

```bash
# install node_modules
npm install
# run dev server
npm run dev

# build production version
npm run build
```

> make sure the correct [adapter](https://kit.svelte.dev/docs/adapters) is set up! (Netlify/Vercel/etc)