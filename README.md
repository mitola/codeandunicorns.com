## To run page locally: 

`hugo server`

## Custom shortcodes:

### Example usage:

**GIST_HTML_ID:**

The GIST_HTML_ID can be found by looking at the .js-version of the original gist: there should be a place where the script creates a div with an id of the form gistXXXXXXXX 

`{{< gist-embed GITHUB_USERNAME GIST_IDENTIFIER GIST_HTML_ID >}}`
