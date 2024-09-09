---
layout: post
title:  "Experimenting with Node.js and MongoDB and Mongoose "
date:   2010-05-25 03:20:08 +0000
categories: node.js
---
Experimenting with Node.js and MongoDb and Mongoose

I came across [Mongoose](http://www.learnboost.com/mongoose/) for [Node.js](http://nodejs.org/). It looks like a promising project but I ran into a bug as soon as I started playing with a simple counter program.  The problem is in the implementation QueryPromise's atomic functions.  Here is a sample program that updates a counter. The three update forms below should all be identical, only the first seems to work with the [version](http://github.com/LearnBoost/mongoose/commit/cb4e171ab8aca56ca1e74f1e7cbba0e77a2eaa0e) I was playing with.

<pre class="brush: python">
// Simple test program to show a problem in QueryPromise
// ['inc','set','unset','push','pushAll','addToSet','pop','pull','pullAll']

var sys = require('sys')
var mongoose = require('mongoose/').Mongoose
var db = mongoose.connect('mongodb://localhost/test');

var Simple = mongoose.noSchema('test',db);
Simple.drop(); 
//should only be one....
var m = new Simple({name:'test', x:0,y:0}).save()
// these should behave the same
Simple.update({name:'test'},{'$inc':{x:1, y:1}}).execute();
Simple.update({name:'test'}).inc({x:1, y:1}).execute();
Simple.update({name:'test'}).inc({x:1}).inc({y:1}).execute();

Simple.find({name:'test'}).each(
     function (doc) {
         sys.puts(JSON.stringify(doc));
     }
).then(
    function(){ // promise (execute after query)
        Simple.close(); // close event loop
    }
);
</pre>

Here is a fixed version of QueryPromise's atomic functions that place the command and arguments in the correct place.  

<pre class="brush: python">
// atomic similar
  
  ['inc','set','unset','push','pushAll',
  'addToSet','pop','pull','pullAll'].forEach(function(cmd){
      QueryPromise.prototype[cmd] = function(modifier){
        if(this.op.name.charAt(0) != 'u') return this;
        if(!this.op.args.length) this.op.args.push({},{});
        if(this.op.args.length == 1) this.op.args.push({});
        for(i in modifier) {
          if(!(this.op.args[1]['$'+cmd] instanceof Object)) this.op.args[1]['$'+cmd] = {};
          this.op.args[1]['$'+cmd][i] = modifier[i];
        }
        return this;
      }
  });
</pre>