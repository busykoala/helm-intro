# Chart structure

The `templates` directory is the most important directory
to start with. All `*.yaml` files in there are rendered
unless the start with an underscore (those are reserved for
[partials and helpers](https://helm.sh/docs/chart_template_guide/named_templates/#partials-and-_-files)).

Values can be used within these templates with
`{{ .Values.path.to.value }}`.
The values are defined in yaml files e.g. the `values.yaml`.

On templating/installing/upgrading multiple value files can be
provided and the last one has priority over the front ones.

Also those variables can be overriden using `--set foo=bar`
in the prompt itself.

For further reading checkout the [helm guidelines](https://helm.sh/docs/chart_template_guide/getting_started/).
