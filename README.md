### Introduction
The unix-toolbox.js is a port of many popular Unix command line tools to Javascript using Mozilla's [emscripten](http://emscripten.org/) project.
It runs completely on the client-side and requires WebWorker support in the client's browser.

### Currently Supported Commands
* [GnuPG](https://github.com/manuels/unix-toolbox.js-gnupg)
  * `gpg` OpenPGP encryption and signing tool
* [ImageMagick](https://github.com/manuels/unix-toolbox.js-imagemagick)
  * `composite` overlaps one image over another.
  * `conjure` interprets and executes scripts written in the Magick Scripting Language (MSL).
  * `convert` convert between image formats as well as resize an image, blur, crop, despeckle, dither, draw on, flip, join, re-sample, and much more.
  * `identify` describes the format and characteristics of one or more image files.
  * `mogrify` resize  an  image, blur, crop, despeckle, dither, draw on, flip, join, re-sample, and much more. Mogrify overwrites the original image file, whereas,
       convert(1) writes to a different image file.
  * `montage` create  a  composite image by combining several separate images. The images are tiled on the composite image optionally adorned with a border, frame,
       image name, and more.
  * `stream` a lightweight tool to stream one or more pixel components of the image or portion of the image to your choice of storage formats.
* [Poppler](https://github.com/manuels/unix-toolbox.js-poppler)
  * `pdfdetach` Portable Document Format (PDF) document embedded file extractor
  * `pdffonts` Portable Document Format (PDF) font analyzer
  * `pdfimages` Portable Document Format (PDF) image extractor
  * `pdfinfo` Portable Document Format (PDF) document information extractor
  * `pdfseparate` Portable Document Format (PDF) page extractor
  * `pdftohtml` program to convert PDF files into HTML, XML and PNG images
  * `pdftoppm` Portable Document Format (PDF) to Portable Pixmap (PPM) converter
  * `pdftops` Portable Document Format (PDF) to PostScript converter
  * `pdftotext` Portable Document Format (PDF) to text converter
* [File](https://github.com/manuels/unix-toolbox.js-file)
  * `file` determine file type
* [XZ Utils](https://github.com/manuels/unix-toolbox.js-xz)
  * `xz` Compress or decompress .xz files
  * `xzdec` Small .xz decompressor

### Example
The Javascript class `Interface` can be used to initiate a WebWorker and communicate with it..
This is an example from ImageMagick's `convert` tool that loads some XML files required by ImageMagick and then loads the image `test.png`, rotates it and converts it to JPEG format:

    <script src="toolbox-base/interface.js"></script>
    <script>
      var convert = new Interface('convert-worker.js');

      // print convert output to console
      convert.on_stdout = function(txt) { console.log(txt); };
      convert.on_stderr = function(txt) { console.log(txt); };

      // load ImageMagick's configuration files
      convert.mkdir('/', 'usr/local/etc/ImageMagick').then(function() {
        convert.addUrl('./src/config/magic.xml',   '/usr/local/etc/ImageMagick', 'magic.xml');
        convert.addUrl('./src/config/coder.xml',   '/usr/local/etc/ImageMagick', 'coder.xml');
        convert.addUrl('./src/config/policy.xml',  '/usr/local/etc/ImageMagick', 'policy.xml');
        convert.addUrl('./src/config/english.xml', '/usr/local/etc/ImageMagick', 'english.xml');
        convert.addUrl('./src/config/locale.xml',  '/usr/local/etc/ImageMagick', 'locale.xml');
        convert.addUrl('./src/config/delegates.xml',  '/usr/local/etc/ImageMagick', 'delegates.xml');

        // load image to convert
        convert.addUrl('demo/test.png', '/', 'test.png');

        convert.allDone().then(function() {
          convert.run('-rotate', '90', '/test.png', '/test-rotated-90.jpg').then(function() {
            convert.getFile('/', 'test-rotated-90.jpg').then(function(contents) {

              var img = document.createElement("img");
              img.src = "data:image/jpeg;base64,"+window.btoa(contents);

              var body = document.getElementsByTagName("body")[0];
              body.appendChild(img);
            });
          });
        });
      });
    </script>

