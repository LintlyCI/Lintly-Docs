# Lintly-Docs

Documentation for [Lintly.com](lintly.com).

View the docs at [https://lintlyci.github.io/Lintly-Docs/](https://lintlyci.github.io/Lintly-Docs/).

## Building the documentation

1. Install dependencies: `pip install -r requirements.txt`
2. Start the server: `mkdocs serve`
3. Go to http://127.0.0.1:8000 in your browser

## Deploying documentation

Lintly documentation is deployed to GitHub Pages. To deploy, run the following:

```
$> mkdocs gh-deploy
```

This will build the documentation, copy the build to the `gh-pages` branch, and push the `gh-pages`
branch to GitHub.
