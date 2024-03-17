# Developer experience

<!-- ## Compatibility with Lit's helpers

This is an non-exhaustive list of expectations that will be updated
empirically.

- You can use `unsafeHTML` and `unsafeCSS`.
-  -->

## Plugins

[**Lit Analyzer**](https://github.com/runem/lit-analyzer) is a must-have collection
of tools that will bring:

- **IDE popups/completion for HTML** and CSS template literals.
- **TypeScript** properties validation for **Custom Elements**.
- HTML **linting**.
- Accessibility hints.
- And much more…

It's available as both a **VSCode** plugin OR a **TypeScript** plugin that will work
in every editor which support the TypeScript language server (Zed, Neovim…).

## Browser devtools comfort

When working with Lit templates, you'll find a lot of "marker" comments in the
produced output, e.g. ` <!--lit-part prGdWBNEVq4=-->`.

It is used notably for hydration and Lit's template client rendering system.

As HTML comments are _mostly_ useful when reading the source code in your text editor,
it's possible to unclutter your browser view while developing.  
For that, you can uncheck <samp>Settings > Preferences > Elements > Show HTML comments</samp> in Chromium-like developer tools.

It's pretty nice to cut the noise like this if you want, especially when
you compare with other markers mechanisms (e.g. CSS scoping…), where
those random strings belong IN your effective markup, not comments.

Be aware that you SHOULD NOT minify and remove comments while building.

For now, comments are not removed with Gracile.

It could be possible to build a comment stripper that preserves Lit's specific markers but not your comments.
