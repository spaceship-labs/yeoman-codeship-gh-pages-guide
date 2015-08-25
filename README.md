## Deploy a yeoman-angular app to gh-pages with Codeship CI

### Delete repository deploy key

### Add deploy key in codeship-> general -> SSH public key to github bot account

### Make an initial push to gh-pages branch

```Shell
git subtree push --prefix dist origin gh-pages
```

### Setup deploy script in codeship

```Shell
git config --global user.email "{{email of the bot account}}"
git config --global user.name "{{name of the bot account}}"
git add dist -f && git commit -m "Build [skip ci]"
git push {{full repo address*}} `git subtree split --prefix dist master`:gh-pages --force
```

* its important that **full repo address** is the complete git repo url not just the name of the remote

### Setup base-href correctly

* Install grunt-processhtml

```Shell
npm install --save-dev grunt-processhtml
```

* Add grunt-task to `gruntfile.js`

```Javascript
processhtml: {
  options: {
    commentMarker: "process"
  },
  dist: {
    files: [{
      expand: true,
      cwd: '<%= yeoman.dist %>',
      src: ['index.html', 'index.html'],
      dest: '<%= yeoman.dist %>'
    }]
  }
}
```

* Add processhtml task to build task right after filerev; should look something like this:

```Javascript
  grunt.registerTask('build', [
    'clean:dist',
    'wiredep',
    'useminPrepare',
    'concurrent:dist',
    'autoprefixer',
    'ngtemplates',
    'concat',
    'ngAnnotate',
    'copy:dist',
    'cdnify',
    'cssmin',
    'uglify',
    'filerev',
    'processhtml',
    'usemin',
    'htmlmin'
  ]);
```

* Add base href in the head of your index.html file

```HTML
<!-- process:[href]:dist /{{repo-name}}/ -->
<base href="/" />
<!-- /process -->
```
