These are the icons you can use if you would like a custom icon. 

## Using docker:

Go to the path: 

    cd /var/lib/docker/volumes/
Enter your volume path, example (this is my mounted /data path): 

    cd node_red_data/_data/

create a folder for the custom icon files:

    mkdir custom_icons
    cd custom_icons
    
Then download te files into the path:

    curl -L https://github.com/edterbak/NodeRed_Heishamon_control/blob/beta-1/icons/icon64x64.png?raw=true -o icon64x64.png
    curl -L https://github.com/edterbak/NodeRed_Heishamon_control/blob/beta-1/icons/icon120x120.png?raw=true -o icon120x120.png
    curl -L https://github.com/edterbak/NodeRed_Heishamon_control/blob/beta-1/icons/icon192x192.png?raw=true -o icon192x192.png
 
Edit the settings.js again. This can be found under: 

    /var/lib/docker/volumes/node_red_data/_data/
 
 Do:
 
    nano /var/lib/docker/volumes/node_red_data/_data/settings.js

Go all the way to the bottom page and find the commented out "//ui:" part. Directly under it, past the following code:

        ui: {
	    path: "/ui/",
	    middleware: function (req, res, next) {
	        var path = require('path');
	        if (['/icon64x64.png', '/icon120x120.png', '/icon192x192.png'].includes(req.url)) {
    		    res.sendFile(path.resolve(path.join('/data/custom_icons', req.url)));
    	    } else {
    	    next();
    	    }
    	}
        },

Thanks to @t21 (tweakers.net) for the tips on this.
