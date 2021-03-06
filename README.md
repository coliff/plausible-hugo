# plausible-hugo theme

According to their web site, *"Plausible Analytics, is a simple, open-source, lightweight (< 1 KB) and privacy-friendly web analytics alternative to Google Analytics"*

This Hugo theme is a dead simple integration between [plausible.io](https://www.plausible.io?ref=github-plausible-hugo) and [gohugo.io](https://www.gohugo.io)

## Usage

- Add this `plausible-hugo` theme component in your theme section in `config.toml` : `theme = ["theme1", ..., "plausible-hugo"]`
- Add a `[params.plausible]` section in your `config.toml` file.
- Call the partial `plausible_head.html` in your own `<head>` section : `{{ partial "plausible_head.html" . }}`

**That's it !** Nothing more needed.

### File `config.toml`

```toml
[params.plausible]
   enable = true  # Whether to enable plausible tracking
   domain = "example.com"  # This is the plausible "domain" name/id in your dashboard
   custom_js_domain = "stats.example.com"  # Whether to serve the script from a custom domain (https://docs.plausible.io/custom-domain) (Optional)
   selfhosted_domain = "myplausible.example.com"  # Self-hosted plausible domain (Optional. "plausible.io" is used if unset)
```

## Custom goals

If you want to use some custom goals, for each goal, you just have to add a snipplet in a partial named `plausible_js.html` that you have to create in your site `/partials` directory

Then, add a straightforward call to those plausible function/goal where you need it in your gohugo code.

### Snipplet example for `plausible_js.html`

```js
function ClickOnTelephoneNumber() {
   plausible('ClickOnTelephoneNumber');
}
```

### Example of integration in your html code

```html
<a href="tel:+331234567890}"
   onclick="ClickOnTelephoneNumber()">
+331234567890
</a>
```

### Using variables for your Goal names

For more flexibility, do not forget that you can use any `{{ $variable }}` or `{{ .variable }}` for your Goal name.

For example, generate your goals using `/data/about.yml` or similar frontmatter, you get the idea.

Then you’ll get 2 goals : `telephoneMobileAbout` & `telephoneHomeAbout`

#### Example : `/data/about.yml`

```yml
about:
  enable : true
  title : My title
  about_item :
  - title : An other item title
    plausible : Mobile
    content : lorem ipsum is better than nothing.
  - title : An other item title 2
    plausible : Home
    content : lorem ipsum is better than nothing 2
```

#### sniplet for `plausible_js.html`

```js
{{- $data := index .Site.Data .Site.Language.Lang }}
{{- if $data.about.about.enable }}
   {{- range $data.about.about.about_item }}
      function telephone{{ .plausible | safeJS }}About() {
          plausible('telephone{{ .plausible | safeJS }}About');
      }
  {{- end }}
{{- end }}
```

## Plausible custom subdomain

If you [use your own subdomain](https://docs.plausible.io/custom-domain) for plausible.io, you just have to give the url in `custom_js_domain` parameter.

## Be carefull if you use Content-Security-Policy

If you have some CSP in your headers, do not forget to allow plausible domains you use.

### CSP example for `index.header`

You can add those 2 domains to your existing `Content-Security-Policy`.

```headers
Content-Security-Policy: [... existing stuff ...] {{ site.Params.plausible.custom_js_domain }} https://plausible.io
```

## Mode server

When you're in `hugo mode server`, the call to plausible.io javascript is disable, so you can dev without bloating your statistics.

## Self Hosting

You can define your self hosted domain address in `config.toml`.

```toml
[params.plausible]
   selfhosted_domain = "myplausible.example.com"
```

## Credits

- Hugo : [www.gohugo.io](https://www.gohugo.io)
- Plausible : [www.plausible.io](https://www.plausible.io?ref=github-plausible-hugo)
  - Plausible documentation : [docs.plausible.io](https://docs.plausible.io?ref=github-plausible-hugo)
  - Plausible community forum : [plausible.discourse.group](https://plausible.discourse.group?ref=github-plausible-hugo)
