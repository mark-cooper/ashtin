ArchivesSpace CLI
===================

This is a work in progress. It is intended to provide a command-line interface for working with the ArchivesSpace backend API. See http://github.com/archivesspace/archivesspace/.

## Getting Started

Install:

    $ npm install as-cli

Log in to your ArchivesSpace instance and save your session key:

    $ as-cli setup

Select your active repository:

    $ as-cli repositories

## Usage

To see all available subcommands:

    $ as-cli

## Using Custom Scripts 

In addition to the built-in commands, you can use a custom script. Your script should assign a single function to the module.exports global object, and that function should take a single argument, which is the activated client.

See https://github.com/lcdhoffman/asapi for more information about working with the api.

    $ as-cli run-script path/to/your/script.js

Example script:

```javascript
module.exports = function(api) {
  api.eachResource(function(resource) {
    var update = false;

    for (var i = 0; i < resource.extents.length; i++) {

      if (resource.extents[i] && resource.extents[i].extent_type == 'linear_feet') {
        resource.extents[i].number = (resource.extents[i].number * 0.3048) + "";
        resource.extents[i].extent_type = "linear_meters";
        update = true;
      }
    }

    if (update) api.updateRecord(resource, function(err, body) {
      if (err) {
        throw (err).
      } else {
        console.log(body);
      }
    });
  });
});
```

I'm still trying to figure out a way for ad-hoc scripts to use require in a way that doesn't require a local node-modules directory. For the time being, if your script requires a 3rd party module, you'll need to make nodejs think your working directory is a project. (i.e., run npm init and install modules locally, or symink to your global node_modules directory).
