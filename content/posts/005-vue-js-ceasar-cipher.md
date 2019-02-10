+++ 
draft = false
date = 2019-02-10T06:29:19+01:00
title = "A Ceasar cipher implemented in Vue.js"
description = "Just showing off my brand new Ceasar cipher that I implemented in Vue.js"
slug = "ceasar-cipher-in-vue-js" 
tags = ["ceasar", "cipher"]
categories = ["CTF", "Vue"]
externalLink = ""
+++

Related to my recent interest in CTF, I've spent a little time to implement a simple [Ceasar cipher](https://en.wikipedia.org/wiki/Caesar_cipher) in [Vue.js](https://vuejs.org/). It allows the user to replace letters with another letter some adjustable number of positions up or down the alphabet.

I did this partly because it's a long time since I've done any Vue-related work and also because I find other online Ceasar cipher sites to be a bit clunky and not as user friendly. I mean, there is no reason to have to click a `Encode` or `Decode` button. Come, on, it's 2019 allready! Once I change the input, I expect things to happen!

## Behold, Ceasar

{{<vue>}}
    <style>
        #app > * {
            width: 100%;
        }
        #app .output {
            background-color: #DDD;
        }

        #app textarea,
        #app .output {
            padding: 10px
        }

        #app input {
            padding: 5px 10px;
        }
    </style>

    <div id="app">
        <h3>Input</h3>
        <textarea v-model="intext" placeholder="Cipher text goes here." rows="6"></textarea>
        <div>
            <input v-model.number="key" type="number" min="-25" max="25">
            <button v-on:click="plus">Up</button>
            <button v-on:click="minus">Down</button>
            <button v-on:click="flip">Flip!</button>
        </div>
        <h3>Output</h3>
        <pre class="output">{{ ciphered }}</pre>
        <h3>Alphabet shift</h3>
        <pre class="output">
{{ alpha }}
&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;&darr;
{{ shiftalpha }}</pre>
    </div>

    <script>
        window.addEventListener("load", function(event) {
            var app = new Vue({
                el: '#app',
                data: {
                    intext: '',
                    key: 3,
                    alpha: 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
                },
                methods: {
                    plus: function(){
                        if ( this.key < 25 ){
                            this.key = this.key + 1
                        }
                    },
                    minus: function(){
                        if ( this.key > -25 ){
                            this.key = this.key - 1
                        }
                    },
                    flip : function(){
                        this.intext = this.ciphered
                        this.key = - this.key
                    },
                    cipher: function(text, key) {

                        var output = ''
                        var code = 0

                        for ( var i = 0; i < text.length; i++ ){

                            code = text.charCodeAt(i)
                            
                            if ( code >= 65 && code <= 90 ) {
                                output += String.fromCharCode( ( code - 65 + key + 26 ) % 26  + 65 )
                            } else if ( code >= 97 && code <= 122 ) {
                                output += String.fromCharCode( ( code - 97 + key + 26 ) % 26  + 97 )
                            } else {
                                output += text.charAt(i)
                            }

                        }
                        return output
                    }

                },
                computed: {
                    ciphered: function () {

                        return this.cipher(( this.intext == '' ? 'Cipher text goes here.' : this.intext ), this.key)
                    },
                    shiftalpha: function() {
                        return this.cipher( this.alpha, this.key )
                    }
                }
            })
        })
    </script>
{{</vue>}}

## Charcodes does the trick

Using the character code, the integer "value" of a letter, simple math can shift uppercase A-Z (97-122) and lowercase a-z (65-90) letters up or down the alphabet.

* [String.fromCharCode()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/fromCharCode)
* [String.prototype.charCodeAt()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/charCodeAt)

## All done

That's it. I just wanted to show off my shiny new toy! If you want to see the source, you can find it in my [blogs source code on GitHub](https://github.com/krilor/blog/blob/master/content/posts/005-vue-js-ceasar-cipher.md)