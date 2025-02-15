# Adding content: notebook, files and static assets

## Content with the CLI

With the [CLI](../../reference/cli.ipynb) installed, run:

```bash
jupyter lite build
```

Any contents found in:

- `{lite-dir}/files/`
- any _content roots_ added via:
  - the CLI flag `--contents`
  - the `#/LiteBuildConfig/contents` in `jupyter_lite_config.json`

Will be:

- copied to the built site under `{output-dir}/files/`
  - may have timestamps changed if `--source-date-epoch` is provided.
- indexed to provide `{output-dir}/api/contents/{subdir?}/all.json`

## Server Contents and Local Contents

When a user changes a server-hosted file, a copy will be made to the browser's storage,
usually in `IndexedDB`. A user's locally-modified copy will take precedence over any
server contents, even if the server contents are newer.

## Customizing Content Storage

By default, all of a user's contents on the same domain will be available to all
JupyterLite instances hosted there. To create separate content stores, change the
`jupyter-lite.json#jupyter-config-data/contentsStorageName` from the default of
`JupyterLite Storage`.

By default, the best available, persistent storage driver will be used. One may force a
particular set of drivers to try with
`jupyter-lite.json#jupyter-config-data/contentsStorageDrivers`. See more about
[local storage drivers](../configure/storage.md).

## Customizing MIME types

[MIME types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types)
drive a great number of JupyterLab's (and therefore JupyterLite's) features. When
uploaded as pre-indexed contents, the build process will usually detect MIME types
correctly.

In the browser, things are a bit trickier: a number of well-known file types (included
everything needed to serve a core JupyterLite site) will be automatically detected when
they are uploaded, but some customization might be required.

The default file types, and any configured via `#/LiteBuildConfig/extra_file_types` will
be merged with the default types into `jupyter-lite.json#jupyter-config-data/fileTypes`,
and these will be used.

```{note}
These will not impact how the JupyterLite UI actually displays files: these are
provided by
[MIME renderer plugins][mime-docs], such as those listed on [PyPI][mime-pypi]

[mime-docs]: https://jupyterlab.readthedocs.io/en/stable/extension/extension_dev.html#mime-renderer-plugins
[mime-pypi]: https://pypi.org/search/?q=&o=&c=Framework+%3A%3A+Jupyter+%3A%3A+JupyterLab+%3A%3A+Extensions+%3A%3A+Mime+Renderers
```

For example, to ensure the `.fasta` file format is served correctly as `text/plain`:

```yaml
# jupyter_lite_config.json
{
  'LiteBuildConfig':
    {
      'extra_file_types':
        {
          'fasta':
            {
              'name': 'fasta',
              'extensions': ['.fasta'],
              'mimetypes': ['text/plain'],
              'fileFormat': 'text',
            },
        },
    },
}
```
