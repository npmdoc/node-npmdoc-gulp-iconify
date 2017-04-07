# api documentation for  [gulp-iconify (v0.6.0)](https://github.com/gavro/gulp-iconify)  [![npm package](https://img.shields.io/npm/v/npmdoc-gulp-iconify.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-gulp-iconify) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-gulp-iconify.svg)](https://travis-ci.org/npmdoc/node-npmdoc-gulp-iconify)
#### 'A mystical CSS icon solution', grunticon-like build system.

[![NPM](https://nodei.co/npm/gulp-iconify.png?downloads=true)](https://www.npmjs.com/package/gulp-iconify)

[![apidoc](https://npmdoc.github.io/node-npmdoc-gulp-iconify/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-gulp-iconify_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-gulp-iconify/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-gulp-iconify/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-gulp-iconify/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Gabrijel Gavranović",
        "email": "gabrijel@gavro.nl",
        "url": "http://gavro.nl"
    },
    "bugs": {
        "url": "https://github.com/gavro/gulp-iconify/issues"
    },
    "dependencies": {
        "del": "~1.1.1",
        "domain": "*",
        "gulp": ">3.0",
        "gulp-sass": "~2.1.0",
        "gulp-svg2png": "~2.0.0",
        "gulp-util": "^3.0",
        "imacss": "~0.3.0",
        "mustache": "~2.0.0",
        "node-sass": "^3.4.1",
        "object-assign": "^4.0.1",
        "path": "*",
        "svgo": "~0.5.0",
        "through": "~2.3.6",
        "through2": "^2.0.0",
        "vinyl-sourcemaps-apply": "^0.2.0",
        "xmldom": "~0.1.19"
    },
    "description": "'A mystical CSS icon solution', grunticon-like build system.",
    "devDependencies": {},
    "directories": {},
    "dist": {
        "shasum": "0d45ed52e690011045653f092b994a493b39e9af",
        "tarball": "https://registry.npmjs.org/gulp-iconify/-/gulp-iconify-0.6.0.tgz"
    },
    "gitHead": "d24edb6964e82ce60bcb8d6e98f1a79b97db463f",
    "homepage": "https://github.com/gavro/gulp-iconify",
    "keywords": [
        "gulpplugin",
        "base64",
        "svg",
        "css",
        "image",
        "grunticon"
    ],
    "license": "MIT",
    "main": "index.js",
    "maintainers": [
        {
            "name": "gavro",
            "email": "gabrijel@gavro.nl"
        }
    ],
    "name": "gulp-iconify",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+ssh://git@github.com/gavro/gulp-iconify.git"
    },
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "version": "0.6.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module gulp-iconify](#apidoc.module.gulp-iconify)
1.  [function <span class="apidocSignatureSpan">gulp-iconify.</span>custom_gulp_sass (options, sync)](#apidoc.element.gulp-iconify.custom_gulp_sass)

#### [module gulp-iconify.custom_gulp_sass](#apidoc.module.gulp-iconify.custom_gulp_sass)
1.  [function <span class="apidocSignatureSpan">gulp-iconify.</span>custom_gulp_sass (options, sync)](#apidoc.element.gulp-iconify.custom_gulp_sass.custom_gulp_sass)
1.  [function <span class="apidocSignatureSpan">gulp-iconify.custom_gulp_sass.</span>logError (error)](#apidoc.element.gulp-iconify.custom_gulp_sass.logError)
1.  [function <span class="apidocSignatureSpan">gulp-iconify.custom_gulp_sass.</span>sync (options)](#apidoc.element.gulp-iconify.custom_gulp_sass.sync)
1.  object <span class="apidocSignatureSpan">gulp-iconify.custom_gulp_sass.</span>compiler



# <a name="apidoc.module.gulp-iconify"></a>[module gulp-iconify](#apidoc.module.gulp-iconify)

#### <a name="apidoc.element.gulp-iconify.custom_gulp_sass"></a>[function <span class="apidocSignatureSpan">gulp-iconify.</span>custom_gulp_sass (options, sync)](#apidoc.element.gulp-iconify.custom_gulp_sass)
- description and source-code
```javascript
function gulpSass(options, sync) {
    return through.obj(function(file, enc, cb) {
        var opts,
            filePush,
            errorM,
            callback,
            result;

        if (file.isNull()) {
            return cb(null, file);
        }
        if (file.isStream()) {
            return cb(new gutil.PluginError(PLUGIN_NAME, 'Streaming not supported'));
        }
        if (!file.contents.length) {
            file.path = gutil.replaceExtension(file.path, '.css');
            return cb(null, file);
        }

        opts = assign({}, options);
        opts.data = file.contents.toString();

        // Ensure 'indentedSyntax' is true if a '.sass' file
        if (path.extname(file.path) === '.sass') {
            opts.indentedSyntax = true;
        }

        // Ensure file's parent directory in the include path
        if (opts.includePaths) {
            if (typeof opts.includePaths === 'string') {
                opts.includePaths = [opts.includePaths];
            }
        }
        else {
            opts.includePaths = [];
        }

        opts.includePaths.unshift(path.dirname(file.path));

        // Generate Source Maps if plugin source-map present
        if (file.sourceMap) {
            opts.sourceMap = file.path;
            opts.omitSourceMapUrl = true;
            opts.sourceMapContents = true;
        }

        //////////////////////////////
        // Handles returning the file to the stream
        //////////////////////////////
        filePush = function filePush(sassObj) {
            var sassMap,
                sassMapFile,
                sassFileSrc,
                sassFileSrcPath,
                sourceFileIndex,
                filteredSources;

            // Build Source Maps!
            if (sassObj.map) {
                // Transform map into JSON
                sassMap = JSON.parse(sassObj.map.toString());
                // Grab the stdout and transform it into stdin
                sassMapFile = sassMap.file.replace('stdout', 'stdin');
                // Grab the base file name that's being worked on
                sassFileSrc = file.relative;
                // Grab the path portion of the file that's being worked on
                sassFileSrcPath = path.dirname(sassFileSrc);
                if (sassFileSrcPath) {
                    //Prepend the path to all files in the sources array except the file that's being worked on
                    for (sourceFileIndex = 0; sourceFileIndex < sassMap.sources.length; sourceFileIndex++) {
                        if (sourceFileIndex !== sassMap.sources.indexOf(sassMapFile)) {
                            sassMap.sources[sourceFileIndex] = path.join(sassFileSrcPath, sassMap.sources[sourceFileIndex]);
                        }
                    }
                }
                // Remove 'stdin' from souces and replace with filenames!
                filteredSources = sassMap.sources.filter(function(src) {
                    if (src.indexOf('stdin') === -1) {
                        return src;
                    }
                });
                sassMap.sources = filteredSources;
                sassMap.sources.unshift(sassFileSrc);
                // Replace the map file with the original file name (but new extension)
                sassMap.file = gutil.replaceExtension(sassFileSrc, '.css');
                // Apply the map
                applySourceMap(file, sassMap);
            }

            file.contents = sassObj.css;
            file.path = gutil.replaceExtension(file.path, '.css');

            var parts = file.path.split('/');
            if(parts.length) {
                parts[parts.length - 1] = parts[parts.length - 1].replace(/^_+/, '');
                file.path = parts.join('/');
            }

            cb(null, file);
        };

        //////////////////////////////
        // Handles error message
        //////////////////////////////
        errorM = function errorM(error) {
            var relativePath = '',
                filePath = error.file === 'stdin' ? file.path : error.fil ...
```
- example usage
```shell
n/a
```



# <a name="apidoc.module.gulp-iconify.custom_gulp_sass"></a>[module gulp-iconify.custom_gulp_sass](#apidoc.module.gulp-iconify.custom_gulp_sass)

#### <a name="apidoc.element.gulp-iconify.custom_gulp_sass.custom_gulp_sass"></a>[function <span class="apidocSignatureSpan">gulp-iconify.</span>custom_gulp_sass (options, sync)](#apidoc.element.gulp-iconify.custom_gulp_sass.custom_gulp_sass)
- description and source-code
```javascript
function gulpSass(options, sync) {
    return through.obj(function(file, enc, cb) {
        var opts,
            filePush,
            errorM,
            callback,
            result;

        if (file.isNull()) {
            return cb(null, file);
        }
        if (file.isStream()) {
            return cb(new gutil.PluginError(PLUGIN_NAME, 'Streaming not supported'));
        }
        if (!file.contents.length) {
            file.path = gutil.replaceExtension(file.path, '.css');
            return cb(null, file);
        }

        opts = assign({}, options);
        opts.data = file.contents.toString();

        // Ensure 'indentedSyntax' is true if a '.sass' file
        if (path.extname(file.path) === '.sass') {
            opts.indentedSyntax = true;
        }

        // Ensure file's parent directory in the include path
        if (opts.includePaths) {
            if (typeof opts.includePaths === 'string') {
                opts.includePaths = [opts.includePaths];
            }
        }
        else {
            opts.includePaths = [];
        }

        opts.includePaths.unshift(path.dirname(file.path));

        // Generate Source Maps if plugin source-map present
        if (file.sourceMap) {
            opts.sourceMap = file.path;
            opts.omitSourceMapUrl = true;
            opts.sourceMapContents = true;
        }

        //////////////////////////////
        // Handles returning the file to the stream
        //////////////////////////////
        filePush = function filePush(sassObj) {
            var sassMap,
                sassMapFile,
                sassFileSrc,
                sassFileSrcPath,
                sourceFileIndex,
                filteredSources;

            // Build Source Maps!
            if (sassObj.map) {
                // Transform map into JSON
                sassMap = JSON.parse(sassObj.map.toString());
                // Grab the stdout and transform it into stdin
                sassMapFile = sassMap.file.replace('stdout', 'stdin');
                // Grab the base file name that's being worked on
                sassFileSrc = file.relative;
                // Grab the path portion of the file that's being worked on
                sassFileSrcPath = path.dirname(sassFileSrc);
                if (sassFileSrcPath) {
                    //Prepend the path to all files in the sources array except the file that's being worked on
                    for (sourceFileIndex = 0; sourceFileIndex < sassMap.sources.length; sourceFileIndex++) {
                        if (sourceFileIndex !== sassMap.sources.indexOf(sassMapFile)) {
                            sassMap.sources[sourceFileIndex] = path.join(sassFileSrcPath, sassMap.sources[sourceFileIndex]);
                        }
                    }
                }
                // Remove 'stdin' from souces and replace with filenames!
                filteredSources = sassMap.sources.filter(function(src) {
                    if (src.indexOf('stdin') === -1) {
                        return src;
                    }
                });
                sassMap.sources = filteredSources;
                sassMap.sources.unshift(sassFileSrc);
                // Replace the map file with the original file name (but new extension)
                sassMap.file = gutil.replaceExtension(sassFileSrc, '.css');
                // Apply the map
                applySourceMap(file, sassMap);
            }

            file.contents = sassObj.css;
            file.path = gutil.replaceExtension(file.path, '.css');

            var parts = file.path.split('/');
            if(parts.length) {
                parts[parts.length - 1] = parts[parts.length - 1].replace(/^_+/, '');
                file.path = parts.join('/');
            }

            cb(null, file);
        };

        //////////////////////////////
        // Handles error message
        //////////////////////////////
        errorM = function errorM(error) {
            var relativePath = '',
                filePath = error.file === 'stdin' ? file.path : error.fil ...
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.gulp-iconify.custom_gulp_sass.logError"></a>[function <span class="apidocSignatureSpan">gulp-iconify.custom_gulp_sass.</span>logError (error)](#apidoc.element.gulp-iconify.custom_gulp_sass.logError)
- description and source-code
```javascript
function logError(error) {
    var message = new gutil.PluginError('sass', error.messageFormatted).toString();
    process.stderr.write(message + '\n');
    this.emit('end');
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.gulp-iconify.custom_gulp_sass.sync"></a>[function <span class="apidocSignatureSpan">gulp-iconify.custom_gulp_sass.</span>sync (options)](#apidoc.element.gulp-iconify.custom_gulp_sass.sync)
- description and source-code
```javascript
function sync(options) {
    return gulpSass(options, true);
}
```
- example usage
```shell
...
    return stream;
});

gulp.task('iconify', ['iconify-convert', 'iconify-fallback', 'iconify-sass'], function() {
    // remove SCSS folder/files if SCSS output is disabled
    if(opts.scssDisabled) {
        if(opts.scssRemoveDir) {
            del.sync([opts.scssOutput]);
        } else {
            del.sync([opts.scssOutput+'/_icons.*.scss']);
        }
    }
});

gulp.start('iconify');
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
