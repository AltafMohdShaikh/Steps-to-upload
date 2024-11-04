# Deploying Vite + React App to GitHub Pages

This guide explains the process of deploying a Vite + React app to GitHub Pages.


## Step 1: Initialize Vite + React Project

1. Create a new Vite project:
   ```bash
   npm create vite@latest my-app --template react
   
## Step 2: Create a new repository on GitHub
- this is where your project would be saved

## Step 3: Initialize Git, commit all the files and push them to your new repo:
 ```bash
git init
git add -A
git commit -m "first commit" 
git branch -M main 
git remote add origin https://github.com/[username]/[repo_name].git # Replace with your username and repo URL
git push -u origin main

```
## Error Handling:
While doing the last step you might get ** Fatal Error ** or any other error saying ** pull first **.

If you Get that error use the following code

*Here’s how to fix it:*

1. **Pull the Remote Changes**:
   - Run the following command to pull the latest changes from the remote branch into your local branch:
     ```bash
     git pull origin main --rebase
     ```
   - The `--rebase` option will apply your local commits on top of the latest commits from the remote branch, helping avoid unnecessary merge commits.

2. **Resolve Any Conflicts (if they appear)**:
   - If there are conflicting files, Git will prompt you to resolve them manually. Open each conflicting file, fix the conflicts, and mark the files as resolved with:
     ```bash
     git add <filename>
     ```
    After adding commit the changes

3. **Complete the Rebase**:
   - If a rebase was required, finalize it with:
     ```bash
     git rebase --continue
     ```

4. **Push Your Changes**:
   - Now, push your changes to the remote branch:
     ```bash
     git push -u origin main
     ```
     
# Now your repo is complete but not deployed, to do so we have to make some changes 

## **Step 1: Set base path in vite.config.ts**

you have to create that folder

If you won’t do this, you get a 404 error for each asset in your project:
``` bash
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  base: "/vite-react-deploy/", // YOUR REPO NAME HERE
});
```
## Step 2: Add a GitHub workflow

Create a `deploy.yml` file inside the `.github/workflows` directory.

Copy and paste this workflow:
``` bash
name: Deploy

on:
  push:
    branches:
      - main

jobs:
  build:
    name: Build
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Setup Node
        uses: actions/setup-node@v3

      - name: Install dependencies
        uses: bahmutov/npm-install@v1

      - name: Build project
        run: npm run build

      - name: Upload production-ready build files
        uses: actions/upload-artifact@v3
        with:
          name: production-files
          path: ./dist

  deploy:
    name: Deploy
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - name: Download artifact
        uses: actions/download-artifact@v3
        with:
          name: production-files
          path: ./dist

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```
## Push those changes
``` bash
git add -A
git commit -m "deploy config" 
git push
```
## Now main step
On your repo page:

- Go to Settings → Actions → General,
- Scroll down to Workflow Permissions,
- Choose Read and Write option, and then save

## Re-run actions
`Actions` → choose a failed deployment → `Re-run failed jobs`.

Wait until in finishes.

## Configure GitHub Pages:
- Go to Settings → Pages
- Under Source, choose "Deploy from branch" and select the "gh-pages" branch.
- Click Save.

## Once the workflow finishes successfully, you'll see a new deployment on your GitHub code Pages. Click the link to access your deployed Vite React application!
