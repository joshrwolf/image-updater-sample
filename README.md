# image-updater-sample

This repo contains an example implementation of auto updating images to the latest tags using the flux toolkit.

The required components running in a cluster are:

* flux: specifically `image-automation-controller`, `image-reflector-controller`, `source-controller`, and `kustomize-controller`

## Setup

Assuming you're running an empty cluster, setup the flux components and the github authentication with the following:

```
flux bootstrap github \
    --components="source-controller,kustomize-controller" \
    --components-extra="image-reflector-controller,image-automation-controller" \
    --read-write-key \
    --owner=joshrwolf \
    --repository=image-updater-sample \
    --branch=main \
    --path="."
```

Flux (`image-automation-controller`) will be pushing to your repo of choice (`joshrwolf/image-updater-sample` in example above), so it will need permissions to do so.  The `bootstrap` command above sets those permissions up as a Github deploy key.

## Image Updater Usage

Once flux is installed, set up image repositories, policies, and commit rules for the desired image.

In this example, `image-update.yaml` defines:

* `ImageRepository`: an image repository to reconcile all available tags (similar to `crane ls $image`)
* `ImagePolicy`: a policy defining how to parse and order all available image tags to determine what "latest" means
* `ImageUpdateAutomation`: instructions for how to commit when a new "latest" image is detected

This repo demonstrates automatically updating a `.ko.yaml` file to publish a new PR everytime a new image is detected for the `defaultBaseImage`.  The definition of "latest" is configured to be based on the latest available tagged date.

When a new image is determined, flux is instructed to commit the update to the `image-updater` branch, and a Github actions workflow then creates/updates the appropriate PR (example [here](https://github.com/joshrwolf/image-updater-sample/pull/1)).  This ensures automatic updates are first reviewed through a PR process.

If desired, flux can also commit directly to `main` (example [here](https://github.com/joshrwolf/image-updater-sample/commit/64807db03241006bd48701d5ce25792dde64a38e)), but implementing automation within a manual PR flow is preferred.

### Custom commit/PR messages

The commit message and author, and PR is completely customizable, see [the flux docs](https://fluxcd.io/docs/guides/image-update/#configure-the-commit-message) for available customizations.
