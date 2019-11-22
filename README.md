# GitHub Actions for Firebase

This Action for [firebase-tools](https://github.com/firebase/firebase-tools) enables arbitrary actions with the `firebase` command-line client.

## Inputs

* `args` - **Required**. This is the arguments you want to use for the `firebase` cli


## Environment variables

* `FIREBASE_TOKEN` - **Required**. The token to use for authentication. This token can be aquired through the `firebase login:ci` command.

* `PROJECT_ID` - **Optional**. To specify a specific project to use for all commands, not required if you specify a project in your `.firebaserc` file.

* `PROJECT_PATH` - **Optional**. The path to `firebase.json` if it doesn't exist at the root of your repository. e.g. `./my-app`

## Example

To authenticate with Firebase, and deploy to Firebase Hosting:

```yaml
name: Test and Deploy Cloud Functions
on:
  push:
    branches:
      - master

env:
  PROJECT_PATH: ./cloud-functions

jobs:
  test:
    name: Test
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repo
        uses: actions/checkout@master
      - name: Install Dependencies
        run: npm --prefix cloud-functions/functions install
      - name: Test Cloud Functions
        run: npm --prefix cloud-functions/functions test
  deploy:
    name: Deploy
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repo
        uses: actions/checkout@master
      - name: Install Dependencies
        run: npm --prefix cloud-functions/functions install
      - name: Deploy to Firebase
        uses: stripysock/firebase-action@stripysock
        with:
          args: deploy
        env:
          FIREBASE_TOKEN: ${{ secrets.FIREBASE_TOKEN }}
```

## License

The Dockerfile and associated scripts and documentation in this project are released under the [MIT License](LICENSE).


### Recommendation

If you decide to do seperate jobs for build and deployment (which is probably advisable), then make sure to clone your repo as the Firebase-cli requires the firebase repo to deploy (specifically the `firebase.json`)
