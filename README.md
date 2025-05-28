### How to use this repository
create directory
```
npm create vite@latest miuwsweeper-vue -- --template vue
cd miuwsweeper-vue
npm install
npm run dev
```
use the directory structure:
```
src/
  components/
    Cell.vue
    Board.vue
  App.vue
  main.js
```

if you dont want to create directory you can clone this repository
```
git clone https://github.com/indhiraya/Design-and-Analysis-Algorithms
```
### How to deploy this repositroy using Github pages
- cd to your project directory
- use this command:
  ```
  git init
  git add .
  git commit -m "Initial commit"
  ```
- make new repository on your github
- then run this command:
  ```
  git remote add origin https://github.com/username/repository-name
  git branch -M main
  git push -u origin main
  npm install --save-dev gh-pages
  npm run build
  npm run deploy
  ```
- then open your url site https://<USERNAME>.github.io/<REPO-NAME>/
  
