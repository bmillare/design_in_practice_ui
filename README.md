# design_in_practice_ui
Visualize the Decision Matrix from a XML or YAML spec that's easy to generate from LLMs and edit by hand

Open `index.html` in a browser, paste DIP-XML or DIP-YAML, and it will render the Direction context plus the decision matrix.

The page is a single HTML file. DIP-XML is the default format and uses the browser's built-in XML parser. DIP-YAML remains available from the format selector and loads `js-yaml` from a CDN so browser parsing handles normal YAML syntax.
