1. Lup Yuen's resume at https://lupyuen.github.io is powered by __JSON Resume__, the interoperable standard format for resumes: https://jsonresume.org

    The resume uses the `StackOverflow` theme: https://www.npmjs.com/package/jsonresume-theme-stackoverflow

1. To create your own resume, clone this repository and install the libraries:

    ```bash
    git clone https://github.com/lupyuen/lupyuen.github.io.git
    cd lupyuen.github.io
    ln -s resume.json my.resume.json
    npm install
    ```

1.  Edit `my.resume.json` with __Visual Studio Code__ and the __JSON Resume Extension__: https://marketplace.visualstudio.com/items?itemName=reflog.jsonresume

1.  Configure the JSON Resume theme in the User Settings for Visual Studio Code (change `/Users/Luppy` to the folder containing `lupyuen.github.io`):

    ```text
    "JSONResume.theme": "/Users/Luppy/lupyuen.github.io/node_modules/jsonresume-theme-stackoverflow",
    ```

1.  The JSON Resume Extension only works for files named `*.resume.json`, hence we created a symbolic link from `my.resume.json` to `resume.json`, which is the filename expected by the command-line tools.

1.  To generate the HTML (`index.html`) and PDF (`lupyuen.pdf`) resume files, run:

    ```bash
    scripts/gen-resume-html.sh
    scripts/gen-resume-pdf.sh
    ```

1.  To publish the HTML and PDF resume files to GitHub Pages (like https://lupyuen.github.io), check out https://pages.github.com/