---
layout: page
title: Contact
permalink: /contact/
show-in-menu: yes
active: active
---


I would like to hear from my readers.

<form id="contact-form" class="form" action="https://getsimpleform.com/messages?form_api_token={{site.simpleform-token}}" method="POST" enctype="multipart/form-data">
        <ul class="contact-ul">
            <li class="contact-li">
                <label class="contact-label" for="name">Name:</label>
                <input type="text" placeholder="Your name" id="name" class="contact-input" name="name" tabindex="1"/>
            </li>
            <li class="contact-li">
                <label class="contact-label" for="email">Email:</label>
                <input type="email" placeholder="Your email" id="email" class="contact-input" name="email" tabindex="2"/>
            </li>
            <li class="contact-li">
                <label class="contact-label" for="message">Message:</label>
                <textarea class="contact-textarea" placeholder="Your message" class="contact-input" rows="4" id="message" name="message" tabindex="3"></textarea>
            </li>
            
        </ul>
        <input type="submit" value="Send" id="submit"/>
        <input type="hidden" name='redirect_to' value="{{site.redirect-to}}" />
        
</form>

This form is setup using [SimpleForm](https://getsimpleform.com){: target="_blank" rel="nofollow"}. You can get your own API token and update the ``simpleform-token`` variable in **_config.yml**. 


The styles for the form is included in this page. I haven't included it in the main age because it has at least 25 lines of css and it is used only on this page. So including it in main css file doesn't make sense.

<style>
form {
    width: 100%;
}
.contact-li {
    list-style: none;
}

.contact-input {
    border:none;
    border-bottom: 1px solid #eee;
    transition-duration: 0.3s;
    width: 100%;
    background-color: transparent;
}

.contact-input:focus {
    outline:none;
    border-bottom: 1px solid #514A9D;

}

.contact-label {
    display: block;
}

ul.contact-ul {
    margin: 0;
    padding: 10px;
    width: 100%;
}

#submit {
    border:none;
    background-color: #514A9D;
    padding: 5px 15px;
    color: #eee;
    opacity: 0.8;
}

#submit:hover {
    opacity: 1;
    cursor: pointer;
}


#contact-form {
    border: 1px solid #aaa;
    display: inline-flex;
    margin-bottom: 1em;
}

</style>