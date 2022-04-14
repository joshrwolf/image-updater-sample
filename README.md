# image-updater-sample

This shows an example of updating a `.ko.yaml` base image everytime an [upstream image](https://github.com/chainguard-dev/dev-toolbox) updates it's tag according to a specific regex rule.

This uses flux's image reflector controller to periodically scan for new images in the source repository.  When a new image is found, a PR is opened using flux's image automation controller with the specific update.
