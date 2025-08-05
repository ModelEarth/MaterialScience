# MaterialScience Webroot

Multi-Region Input-Output (MRIO) using 721 factors across global trade industries

TO DO: [Reduce size of products repo](https://github.com/ModelEarth/products/issues/2) by moving data details to products-data repo.

### Start webroot

Run the following in your local "MaterialScience" folder to start an http server on port 8888

	python -m http.server 8888

Then view pages at:

[localhost:8888/products](http://localhost:8888/products/)  
[localhost:8888/useeio.js](http://localhost:8888/useeio.js/footprint/)  
[localhost:8888/io](http://localhost:8888/io/)  
[localhost:8888/localsite](http://localhost:8888/localsite/)   
[localhost:8888/profile](http://localhost:8888/profile/)  
[localhost:8888/profile](http://localhost:8888/profile/footprint/sample.html) - Combo sample  

Start a virtual environment with Claude Code CLI in the MaterialScience folder.

MacOS

	python3 -m venv env
	source env/bin/activate
	npx @anthropic-ai/claude-code

WindowsOS

	python -m venv env
	env\Scripts\activate
	npx @anthropic-ai/claude-code

<!--
https://www.protolabs.com/
-->