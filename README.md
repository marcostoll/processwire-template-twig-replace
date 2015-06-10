# ProcessWire Module "Template Twig Replace" #

This module offers you template devlopment using the [Twig templating engine](http://twig.sensiolabs.org/).

Instead of *contaminating* your template's and chunk's markup with php code, you would have the [Twig templating syntax](http://twig.sensiolabs.org/doc/templates.html) at hand.

Old school php templating:

	<body>
		<?php if ($page->children()->getTotal() > 0): ?> 
		<div class="subnavi">
			<ul>
				<?php foreach ($page->children() as $child): ?>
				<li><a href="<?php print $child->url; ?>"><?php print $child->title; ?></a></li>
				<?php endforeach; ?>
			</ul>	
		</div>
		<?php endif; ?>	
	
		<h1><?php print $page->title; ?></h1>
	</body>

Twig-style templating:

	<body>
		{% if page.children().getTotal() > 0 %}
		<div class="subnavi">
			<ul>
				{% for child in page.children() %}
				<li><a href="{{ child.url }}">{{ child.title }}</a></li>
				{% endfor %}
			</ul>	
		</div>
		{% endif %}
	
		<h1>{{ page.title }}</h1>
	</body>

## Installing the module ##

Just copy the module files into you `site/modules/` folder or downoload it via the ModuleManager.  
Your directory structure should look like this:  

site/  
|-> modules/  
|--|-> TemplateTwigReplace  
|--|--|-> Twig-1.15.0/  
|--|--|--|-> Twig/  
|--|--|-> README.md  
|--|--|-> TemplateTwigReplace.module  
 
After deploying the module files go to **Setup/Modules** in your ProcessWire backend. You should find the **Template Twig Replace** module in the **Template** section. Hit install and be ready. 

## Configuring the module ##

The **Template Twig Replace** module offers a small set of configuration options. The module will be installed with ready-to-start default values.

1. Auto-import fuel  
List the names of the magic ProcessWire fuel variables (page, pages, config, session, input, user).  
See [ProcessWire API Variables](http://processwire.com/api/variables/) for further details.  

2. Don't render these templates with twig
A list of templates (i.e admin or Formbuilder templates) that shouldn't be rendered with twig.  

2. Enable Twig Cache  
Activate the cache option for storing compiled Twig templates.  
Speeds up your template rendering. Maybe deactivated when in developing mode.  

3. Twig Cache Directory  
Define the name of the directory storing the Twig template cache. 

4. Auto Reload Twig Cache  
Invalid compiled template cache file's if template source file is updated.

5. Auto Escape Twig variables  
Define a automatic escape strategy for Twig handling template output. Should be turned of in most cases due to ProcessWire handling output escaping before.

Options 2-5 will be passed as [environment options](http://twig.sensiolabs.org/doc/api.html#environment-options) to the Twig render engine. Be sure to consult the [documentation](http://twig.sensiolabs.org/doc/api.html#environment-options) for further information.

## Dependancies ##

The **Template Twig Replace** module uses and depends on the [Twig libraries](http://twig.sensiolabs.org/). The module will currently be shipped with Twig v1.15.0 which can also be retrieved [here](http://twig.sensiolabs.org/doc/intro.html#installation).

Future versions of the **Template Twig Replace** module may use newer versions of Twig but will allways be deployed with all necessary files included.

## Module Interaction ##

**Template Twig Replace** can be used as stand-alone module, but it seamlessly integrates with the[**Template Data Providers**](https://github.com/marcostoll/processwire-template-data-providers) module.

All variables provided by custom **PageDataProvider** and **ChunkDataProvider** class will be accessible in yout Twig templates as well.

A template example:

	<body>
		{{ page.renderChunk('/includes/subnavi.twig', page) }}
	
		<h1>{{ page.title }}</h1>

		<!-- assuming the PageDataProvider defined $this->page->foo in populate() -->
		<div>{{ page.foo }}</div>

		<!-- assuming the PageDataProvider defined $this->bar in populate() -->
		<div>{{ bar }}</div>
	</body>

A chunk example:

	<!-- assuming the ChunkDataProvider defined $this->hasChildren in populate() -->
	{% if hasChildren %}
	<div class="subnavi">
		<ul>
			<!-- assuming the ChunkDataProivder defined $this->parent = $context[0] in setContext() -->
			{% for child in parent.children() %}
			<li><a href="{{ child.url }}">{{ child.title }}</a></li>
			{% endfor %}
		</ul>	
	</div>
	{% endif %}

## Using the twig file extension ##

When using Twig templates it is best to configure ProcessWire to use the `twig` file extension to get maxmimum support from your ide.

ProcessWire defines the default `php`file extensions for templates in its core config (`wire/config.php`, option `$config->templateExtension`). But you don't have to mess with the core files. Just add the follwing line to your site config (`site/config.php`):

	$config->templateExtension = 'twig';

**Attention**:  
Be sure to remane the default admin template from `site/templates/admin.php` to `site/templates/admin.twig`! Otherwise your ProcessWire backend won't work if you've change the template extension option.

## Accessing the twig environment from outside the module ##

There are a couple of reasons to access the twig environment from outside the module (e.g. to register custom filters).

To do this the module offers a public method `getTwig()` that returns the `Twig_Environment` instance. The module itself is defined as autoloading, so it should be accessible via the modules registry at all times. The Twig autoloader will be registered as well.  
Use the following code:

	// retrieve the Twig_Environment instance
	$twig = wire('modules')->get('TemplateTwigReplace')->getTwig();
	
	// manipulate the twig environment
	// example code taken from http://twig.sensiolabs.org/doc/advanced.html#filters
	$filter = new Twig_SimpleFilter('rot13', function ($string) {
	    return str_rot13($string);
	}); 
	$twig->addFIlter($filter);

## Honorable mentions ##

The templates was inspired by the **[Template Twig](http://modules.processwire.com/modules/template-twig/)** module by **[porl](http://processwire.com/talk/user/575-porl/)**.  
Find forum support [here](http://processwire.com/talk/topic/1421-twig/). 


## License

This module is released under the MIT License. See [LICENSE.txt]LICENSE.txt). 