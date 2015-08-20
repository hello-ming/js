# js
casper = require("casper").create({
    pageSettings: {userAgent:'spider'},
    viewportSize: {width: 800, height: 600},
    verbose:true,
    logLevel: "info"
});

casper.on('remote.message', function(msg) {
    this.echo('remote message caught: ' + msg);
})

function parseWB(wb){
    //return {
        //'title' : wb.querySelectorAll('div[node-type="feed_list_content"]')[0].innerHTML,
        //'forward' : wb.querySelectorAll('span[node-type="forward_btn_text"]')[0].innerHTML,
        //'comment' :wb.querySelectorAll('span[node-type="comment_btn_text"]')[0].innerHTML,
        //'like' : wb.querySelectorAll('span[node-type="like_status"]')[0].getElementsByTagName('em')[0].innerHTML
    //};
}

function getMsgNum(){
    //return [document.querySelectorAll('div[node-type="feed_list_content"]').length,
        //document.querySelectorAll('span[node-type="comment_btn_text"]').length
        //];
        var wbs = document.querySelectorAll('div.WB_feed_type');
        return Array.prototype.map.call(wbs, function(wb){
            return {
                'title' : wb.querySelectorAll('div[node-type="feed_list_content"]')[0].innerHTML,
                'forward' : wb.querySelectorAll('span[node-type="forward_btn_text"]')[0].innerHTML,
                'comment' :wb.querySelectorAll('span[node-type="comment_btn_text"]')[0].innerHTML,
                'like' : wb.querySelectorAll('span[node-type="like_status"]')[0].getElementsByTagName('em')[0].innerHTML
            };
        });
}

var preStr = '*******';
function customEcho(msg){
    casper.echo(preStr + msg);
}

//casper.start('http://weibo.com/u/2063264115?topnav=1&wvr=6&topsug=1#_loginLayer_1440079176021');
casper.start('http://weibo.com/u/2063264115');

    casper.waitForSelector('h1.username', function() {
        customEcho(this.getCurrentUrl());
        customEcho(this.getTitle());
        require('utils').dump(this.evaluate(getMsgNum));
        this.scrollToBottom();
    });
    casper.thenBypass(3);

    casper.wait(2000, function() {
        customEcho(this.evaluate(getMsgNum));
        this.scrollToBottom();
    });
    casper.wait(2000, function() {
        customEcho(this.evaluate(getMsgNum));
    });
    casper.wait(2000, function(){
        customEcho(this.getCurrentUrl());
        this.capture("bo.png");
    });

    casper.run(); 
