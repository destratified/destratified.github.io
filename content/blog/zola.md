+++
title = "zola pt1"
date = "2025-12-16"
+++



# zola

figuring out zola with the ability to use my git folders and update remotely hosting on giyhub pages

test test test

had to add:
git config --global --add safe.directory /github/workspace

that didn't work so i set the action flow to this instead:


name: Zola on GitHub Pages

on: 
 push:
  branches:
   - main

jobs:
  build:
    name: Publish site
    runs-on: ubuntu-latest
    steps:
    - name: Checkout main
      uses: actions/checkout@v4
    - name: Build and deploy
      uses: shalzz/zola-deploy-action@v0.21.0
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
