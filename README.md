# gulp-rev-collector-v

same with `gulp-rev-collector` but add md5 after filename, work with `gulp-rev-v`

> Static asset revision data collector from manifests, generated from different streams, and replace their links in html template.

## Install

```sh
$ npm install --save gulp-rev-collector-v
```

## Usage

We can use [gulp-rev](https://github.com/sindresorhus/gulp-rev) to cache-bust several assets and generate manifest files for them. Then using gulp-rev-collector we can collect data from several manifest files and replace links to assets in html templates.

```js
var gulp         = require('gulp');
var revV = require('gulp-rev-v');

gulp.task('css', function () {
    return gulp.src('src/css/*.css')
        .pipe(revV())
        .pipe(gulp.dest('dist/css'))
        .pipe( rev.manifest() )
        .pipe( gulp.dest( 'rev/css' ) );
});

gulp.task('scripts', function () {
    return gulp.src('src/js/*.js')
        .pipe(revV())
        .pipe(gulp.dest('dist/js'))
        .pipe( rev.manifest() )
        .pipe( gulp.dest( 'rev/js' ) );
});

...

var revCollectorV = require('gulp-rev-collector-v');
var minifyHTML   = require('gulp-minify-html');

gulp.task('rev', function () {
    return gulp.src(['rev/**/*.json', 'templates/**/*.html'])
        .pipe( revCollector({
            replaceReved: true,
            dirReplacements: {
                'css': '/dist/css',
                '/js/': '/dist/js/',
                'cdn/': function(manifest_value) {
                    return '//cdn' + (Math.floor(Math.random() * 9) + 1) + '.' + 'exsample.dot' + '/img/' + manifest_value;
                }
            }
        }) )
        .pipe( minifyHTML({
                empty:true,
                spare:true
            }) )
        .pipe( gulp.dest('dist') );
});
```

### Options

#### replaceReved

Type : `Boolean`

You set a flag, replaceReved, which will replace alredy replaced links in template's files. Default value is `false`.

#### dirReplacements

Specifies a directories replacement set. [gulp-rev](https://github.com/sindresorhus/gulp-rev) creates manifest files without any info about directories. E.c. if you use dirReplacements param from [Usage](#usage) example, you get next replacement:

```
"/css/style.css" => "/dist/css/style-1d87bebe.css"
"/js/script1.js" => "/dist/script1-61e0be79.js"
"cdn/image.gif"  => "//cdn8.example.dot/img/image-35c3af8134.gif"
```

#### revSuffix

Type : `String`

It is pattern for define reved files suffixes. Default value is '-[0-9a-f]{8,10}-?'. This is necessary in case of e.c. [gulp-rename](https://github.com/hparra/gulp-rename) usage. If reved filenames had different from default mask.


### Works with gulp-rev-collector

- [gulp-rev](https://github.com/sindresorhus/gulp-rev)

## License

[MIT](http://opensource.org/licenses/MIT) © [Oleksandr Ovcharenko](mailto:shonny.ua@gmail.com)
