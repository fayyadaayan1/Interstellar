name: "JFrog Integration Example"
on: push

jobs:
  build:
     runs-on: ubuntu-latest
       
     steps:
       - name: Checkout
         uses: actions/checkout@v3
         
       - name: Setup JFrog CLI
         uses: jfrog/setup-jfrog-cli@v4
         env:  
           JF_URL: ${{ vars.JF_URL }}
           JF_ACCESS_TOKEN: ${{ secrets.JF_ACCESS_TOKEN }}
           
       - name: Setup Node npm
         uses: actions/setup-node@v3
           
       - name: Set CLI Config
         run: jf npm-config --global=true --repo-resolve=p-npm --repo-deploy=p-npm
           
       - name: Install Deps
         run: jf npm install
           
       - name: Run tests
         run: npm test
         
       - name: Publish
         run: jf npm publish
         
       - name: Publish Build info With JFrog CLI
         run: |
          # Collect environment variables for the build
          jf rt build-collect-env
          # Collect VCS details from git and add them to the build
          jf rt build-add-git
          # Publish build info
          jf rt build-publish
