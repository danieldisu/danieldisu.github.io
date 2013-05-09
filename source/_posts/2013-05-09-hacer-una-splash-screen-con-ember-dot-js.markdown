---
layout: post
title: "Hacer una Splash screen con Ember.js"
date: 2013-05-09 18:49
comments: true
categories: [javascript, js, Ember.js, javascriptMVC]
---

Puede darse el caso de que queramos incluir una pantalla mostrando el logo de nuestra empresa o aplicación a la hora de entrar en nuestra aplicación de Ember.js, lo cual si no estuviésemos usando ningún tipo de framework, sería mostrarlo mediante CSS y con una sencilla función de Javascript ( ayudándonos de jQuery ) lo ocultaríamos.

{% codeblock lang:html %}
	<div id="splash">
		<img src="splash.jpg"/>
	</div>
{% endcodeblock %}

{% codeblock lang:javascript %}
	$("#splash").fadeOut("slow");
{% endcodeblock %}

El problema viene cuando necesitamos usar las animaciones que nos proporciona jQuery con Ember, debido a que el <a href="https://github.com/emberjs/ember.js/blob/v1.0.0-rc.3/packages/ember-metal/lib/run_loop.js#L183">RunLoop</a> de Ember interceptará todas las llamadas asincronas que realicemos en nuestra aplicación, entre otras las animaciones mediante jQuery.

La solución es usar los métodos de los que nos provee Ember para lidiar con el Runloop, en este caso usaremos el método Ember.run.later, el cual actúa igual que el método nativo setTimeout().

En este ejemplo, he creado una vista para poder tener el Splash en un template aparte: 

{% codeblock lang:javascript %}
// Subvista creada en ApplicationView
splashView : Ember.View.extend({
	splashTime : 2000,
	init : function(){
		this._super();
		Ember.run.later(function(){
				$("#splash").fadeOut("slow");
		}, this.splashTime);
	},
	defaultTemplate : Ember.Handlebars.compile(splashTemplate),
})
{% endcodeblock %}

y en el template de Application solo mostramos nuestro Splash si el usuario está visitando la pagina por primera vez, para ello utilizo una cookie : 

{% codeblock lang:javascript %}
	{% raw %}
	{{#if firstVisit}}
		{{view view.splashView}}
	{{/if}}
	{% endraw %}
{% endcodeblock %}

{% codeblock lang:javascript %}
var ApplicationController = Ember.Controller.extend({
	firstVisit : true,
	init : function(){
		this._super();
		var options = $.cookie('options');
		this.isFirstVisit(options);
	},
	isFirstVisit : function(options){
		//Comprobamos si es la primera visita a partir de la cookie en caso de que sea la primera visita mostramos el Splash
		var isTheFirstVisit = options.firstVisit;
		if(isTheFirstVisit){
			options.firstVisit = false;
			//Actualizamos la cookie indicandole que no es la primera visita
			$.cookie('options', options);
		}
		return isTheFirstVisit;
	}
});
{% endcodeblock %}