---
title: Cloud Amazon Book Library

layout: page
category: wiki
---

This page describes the setup of an S3 based file storage with corresponding management functions via a shell script, connected via a Lambda function and API calls.

## Server file setup
    {your-bucket-name}       
     |--authority_check
     |  |--user.txt
     |--books            

## Client file setup
    {web-host-folder}       
     |--index.html
     |--get_booklist.js
     |--styles.css

## Setup

### S3 File hierarchy, roles and Login
The first thing that needs to be done is the creation of an S3 bucket and login possibilities to access objects in said bucket.

1. Create the server-side file hierarchy (specified above), by creating a new bucket and setting up the folders within. The "user.txt" file can be empty. Additionally, [enable CORS](https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html#how-do-i-enable-cors), so the files inside the bucket can be accessed from your test-domaint (most likely "localhost") and from the "real" domain.
    <CORSConfiguration>
     <CORSRule>
       <AllowedOrigin>http://example.com</AllowedOrigin>

       <AllowedMethod>PUT</AllowedMethod>
       <AllowedMethod>GET</AllowedMethod>

       <AllowedHeader>*</AllowedHeader>
     </CORSRule>
     <CORSRule>
       <AllowedOrigin>http://localhost:8080</AllowedOrigin>

       <AllowedMethod>PUT</AllowedMethod>
       <AllowedMethod>GET</AllowedMethod>

       <AllowedHeader>*</AllowedHeader>
     </CORSRule>
    </CORSConfiguration>
2. Create a Google OAuth 2.0 (OpenID Connect) "Application" (for details see [here](http://docs.aws.amazon.com/STS/latest/UsingSTS/web-identity-federation.html#web-identity-federation-manual) and [here](https://developers.google.com/identity/protocols/OpenIDConnect)).
2.1 Visit the [Google Developers Console](https://console.developers.google.com/) and create a new project.
2.2 By default, some APIs are already active for new projects. Go to "APIs & auth > APIs" to deactivate them.
2.3 Follow the steps described [on the official Google page](https://developers.google.com/identity/protocols/OpenIDConnect).
2.4 During creation, when entering the Authorized JavaScript origins, be sure to differentiate between http and https connections. Think carefully what you want to enable - https was *needed* for my testing on localhost.
    http://example.com
    http://localhost:8080
    https://localhost:8080
3. [Create an AWS IAM Role for this login option](http://docs.aws.amazon.com/IAM/latest/UserGuide/roles-creatingrole-identityprovider.html#roles-creatingrole-identityprovider-oidc-console), that will be used to specify what authorizations a user has once he logs in with Google on your website. This is only the first of two roles that we will create: the first one will be for the "manager", the second role will be for the "users" of the website.
3.1 In the AWS IAM Console, create a new role for Identity Provider Access.
3.2 Below the Dropdown in which you can choose the identity provider, another field exists and its name changes depending on the selected identity provider. In this field, enter the Client ID from the Google console.
3.3 In the "conditionals", you can enter optional parameter to filter which Google Users can assume this role. Currently, only one option can be chosen: "accounts.google.com:sub" [explained here](https://developers.google.com/identity/protocols/OpenIDConnect#obtainuserinfo) which is the unique ID of each Google account. To determine mine, I went to the [Goolge+ Homepage](https://plus.google.com/u/0/) and copied the link url that lead to my Profile.
3.4 With this data, create the user role. It could look similar to this:
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": "sts:AssumeRoleWithWebIdentity",
          "Principal": {
            "Federated": "accounts.google.com"
          },
          "Condition": {
            "StringEquals": {
              "accounts.google.com:sub": "148555235438760516",
              "accounts.google.com:aud": "1234569608760909-9talb99063542.apps.googleusercontent.com"
            }
          }
        }
      ]
    }
3.5 Create a new policy that will contain the actual access rights that the user will have once he has "assumed" the Web Identity via Google Login. To do this, go to the AWS IAM Console and create the policy:
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "Stmt123568763000",
                "Effect": "Allow",
                "Action": [
                    "s3:GetObject"
                ],
                "Resource": [
                    "arn:aws:s3:::bucketname/books/*",
                    "arn:aws:s3:::bucketname/authority_check/user.txt"
                ]
            }
        ]
    }

Now a user can login in with his google credentials and download files.

### Client side Login
Now that the s3 side of things has been set up, it is time to create the client HTML that will mostly consist of a big friendly "Login" button. 

1. An example HTML, that let's you log in and out with google, could look like this:
    <html>
    <head>
      <meta name="google-signin-clientid"     content="12345978899-ffbofjh76467542lb2g9dda.apps.googleusercontent.com">
      <meta name="google-signin-scope"        content="profile email">
      <meta name="google-signin-cookiepolicy" content="single_host_origin">
      <style type="text/css">
        #signOut { display: none; }
      </style>
        <script>
        function onSignIn(googleUser) {
          // Useful data for your client-side scripts:
          var profile = googleUser.getBasicProfile();
          console.log("Name: " + profile.getName());
          console.log("Image URL: " + profile.getImageUrl());
          console.log("Email: " + profile.getEmail());
          document.getElementById('signOut').style.display = 'inline';
        };

          function signOut() {
            var auth2 = gapi.auth2.getAuthInstance();
            auth2.signOut().then(function () {
              console.log('User signed out.');
              document.getElementById('signOut').style.display = 'none';
            });
          }
        </script>

        <script src="https://apis.google.com/js/platform.js" async defer></script>
    </head>
    <body>
      <div class="g-signin2" data-onsuccess="onSignIn" data-theme="dark"></div>
      <a id="signOut" href="#" onclick="signOut();">Sign out</a>
    </body>
    </html>
2. So the basic login via google works, we can even access the users email and profile picture - for a nice display in our website. Still missing however is the connection to Amazon S3. In order to utilize this, the HTML from above needs to be enhanced:
    <html>
    <head>
      <meta name="google-signin-clientid"     content="9346994t-3s929talb2g95pd23ttie.apps.googleusercontent.com">
      <meta name="google-signin-scope"        content="profile email">
      <meta name="google-signin-cookiepolicy" content="single_host_origin">
      <style type="text/css">
        #signOut { display: none; }
      </style>
        <script>
        var s3 = {};
        var usertype = '';
        function setUserType(userType) {
          usertype = userType;
          if(!Boolean(usertype)) {
            console.log('Logged in with Google, but no access to S3');
          } else {
            console.log('Logged in as ' + usertype);
          }
        }

        function onSignIn(googleUser) {
          // Useful data for your client-side scripts:
          var profile = googleUser.getBasicProfile();
          console.log("Name: " + profile.getName());
          console.log("Image URL: " + profile.getImageUrl());
          console.log("Email: " + profile.getEmail());
          document.getElementById('signOut').style.display = 'inline';

          var id_token = googleUser.getAuthResponse().id_token;
          AWS.config.credentials = new AWS.WebIdentityCredentials({
              RoleArn: 'arn:aws:iam::1234567899474:role/UserRoleName',
              WebIdentityToken: id_token // NOT access_token
          });
          AWS.config.region = 'eu-west-1';
          s3 = new AWS.S3;
          s3.getObject({
              Bucket: 'bucketname',
              Key: 'authority_check/user.txt'
          }, function(err, data) {
              if (!Boolean(err)) {
                  // Do stuff
              } else {
                  // Role could not be assumed, user not authorized
              }
          });
        };

          function signOut() {
            var auth2 = gapi.auth2.getAuthInstance();
            auth2.signOut().then(function () {
              console.log('User signed out.');
              document.getElementById('signOut').style.display = 'none';
            });
          }
        </script>
      <script src="https://sdk.amazonaws.com/js/aws-sdk-2.1.26.min.js" async defer></script>
        <script src="https://apis.google.com/js/platform.js" async defer></script>
    </head>
    <body>
        <div class="g-signin2" data-onsuccess="onSignIn" data-theme="dark"></div>
        <a id="signOut" href="#" onclick="signOut();">Sign out</a>
    </body>
    </html>

### Management via Shell script

This is currently still being worked on ...
An example lambda JSON could look like this:
    {
      "file_in": "Das Test Buch Neu.epub",
      "file_out": "Das Test Buch Neu.epub",
      "title": "Test Titel",
      "author": "Test çAuthor",
      "author_sort": "çAuthor, Test",
      "genre": "Sachbuch"
    }

### AWS Lambda Function
The AWS Lambda function is used to call ebook-meta (The Calibre binary) without the need for a special server. Since the [64bit Versions of Calibre](http://download.calibre-ebook.com/) work perfectly fine on a normal Amazon Linux instance, they work also in the context of an AWS lambda function.
You only have to unpack the binary tarball and include it in your lambda package.
An example package could be set up by creating a folder
    mkdir lambda_function
and then calling the npm installer to include the [async](https://github.com/caolan/async) module
    cd lambda_function; npm install async
and copying the calibre tarball contents to a folder named `calibre`. 
Add an `index.js` file to the folder and you are done. To upload the lambda function as a package, you have to zip it:
    zip -r ../Lambda_Package.zip .

An example index.js file could look like this:
    var exec  = require('child_process').exec;
    var aws   = require('aws-sdk');
    var fs    = require('fs');
    var async = require('async');

    exports.handler = function(event, context) {
        function event_property_to_var(event, property_name) {
            if (event.hasOwnProperty(property_name)) {
              return event[property_name];
            } else {
              return '';
            }
        }
        
        var file_in     = event_property_to_var(event,'file_in');
        var file_out    = event_property_to_var(event,'file_out');
        var title       = event_property_to_var(event,'title');
        var author      = event_property_to_var(event,'author');
        var author_sort = event_property_to_var(event,'author_sort');
        var genre       = event_property_to_var(event,'genre');
        var update      = true;

        if (title === '' || author === '' || author_sort === '' || genre === '') {
          update = false;
        }

        var s3 = new aws.S3({params: {Bucket: 'bucketname'}});
            async.waterfall(
                [
                    function(callback) {
                        if (file_in === '' || file_out === '') {
                          callback('Error','file_in or file_out not provided',null);
                        }
                        callback(null, '');
                    },
                    function(dummy_param, callback) {
                        function s4() {
                          return Math.floor((1 + Math.random()) * 0x10000).toString(16).substring(1);
                        }
                        var tmp_guid = s4() + s4() + s4() + '-' +s4() + s4() + s4();
                        console.log('TempGuid: ' + tmp_guid);
                        callback(null, tmp_guid);
                    },
                    function(guid, callback) {
                        var filename = '/tmp/' + guid + '.' + file_in.split('.').pop();
                        var file   = fs.createWriteStream(filename);
                        var stream = s3.getObject({Key: 'books/' + file_in}).createReadStream();
                          stream.pipe(file).on('end', function() {
                            //Nothing
                          }).on('error', function() {callback('Error', 'Could not read file from S3 to /tmp', filename); });
                          console.log('Opened' + file_in + ' to ' + filename);
                          callback(null, filename); //Das ruft jetzt das CMD auf
                    },
                    function(tmp_filename, callback) {
                        var command = 'export LANG=en_US.UTF-8;./calibre/ebook-meta ' + tmp_filename;
                        if (update === true) {
                          // Write new data to file
                          command += ' -t "' + title + '"';
                          command += ' -a "' + author + '"';
                          command += ' --author-sort="' + author_sort + '"';
                          command += ' --tags="' + genre + '"';
                          command += ' -c "" -r "" -p "" -k "" -d "" -l "" --isbn "" -s "" --category ""';
                        }
                        console.log('Command' + command);
                        callback(null, tmp_filename, command);
                    },
                    function(tmp_filename, command, callback) {
                        var stdout = [],
                            stderr = [];
                        child = exec(command,{encoding:'utf8'},function(error) {
                            if (error) {
                              callback(error, 'ebook-meta exited with non-zero exit status', stderr);
                            }
                        });
                        console.log('Child Exec created');

                        child.stdout.on('data', function(data) {
                            console.log(data);
                            stdout.push(data);
                        });

                        child.stderr.on('data', function(data) {
                            console.error(data);
                            stderr.push(data);
                        });

                        child.on('close', function (code) {
                          if (code !== 0) {
                            callback('Error', 'ebook-meta exited with non-zero exit status', stderr);
                          }
                          callback(null, tmp_filename, stdout);
                        });
                    },
                    function(tmp_filename, stdout, callback) {
                      if (update !== true) {
                        // Data was only read from file
                        callback(null, 'Data was read', stdout);
                      } else {
                        // Data was written to the file
                        var stream  = fs.createReadStream(tmp_filename);
                        s3.putObject({Key: 'books/' + file_out, Body: stream}, function (err) {
                          if (err) {
                            callback(err, 'Could not put file back to s3', null);
                          }
                          callback(null, file_out + ' successfully written', null);
                        });                  
                      }
                    }
                ],
                function (err, msg, data) {
                  console.log('Callback Function Reached');
                  console.log('Err: ' + err);
                  console.log('Msg ' + msg);
                  console.log('Data ' + data);
                  var result = {};
                  result.update = update;
                  result.msg  = msg;
                  result.data = data;
                  if (err) {
                    result.status = 1;
                    var out = '';
                    try {
                      out = msg + JSON.stringify(data);
                    } catch(err) {
                      out = msg;
                    }
                    context.fail(out);
                  } else {
                    result.status = 0;
                    context.succeed(result);
                  }
                }
            );
    }

### Finalization

After having set up all the details, be sure to remove localshost from the buckt CORS configuration as well as from the Google Console Details.

## Sources
- [Alphanumeric Sorting for an Array of Objects](http://stackoverflow.com/questions/26323317/natural-sort-array-of-objects-multiple-columns-reverse-etc)
- [AWS S3 Enable CORS for a bucket](https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html#how-do-i-enable-cors)
- [AWS Web Identity Federation](http://docs.aws.amazon.com/STS/latest/UsingSTS/web-identity-federation.html#web-identity-federation-manual)
- [Google OAuth 2.0 Setup](https://developers.google.com/identity/protocols/OpenIDConnect)
- [Google Login Button](https://developers.google.com/identity/sign-in/web/sign-in)
- [Online Button Image Generator](http://buttonoptimizer.com)
- [AWS Web Identity Federation - Configuration in the Browser](http://docs.aws.amazon.com/AWSJavaScriptSDK/guide/browser-configuring-wif.html)
- [AWS IAM Role Creation for Web Identity Federation](http://docs.aws.amazon.com/IAM/latest/UserGuide/roles-creatingrole-identityprovider.html#roles-creatingrole-identityprovider-oidc-console)
- [AWS S3 Pre-Signed URLs](http://docs.aws.amazon.com/AmazonS3/latest/dev/ShareObjectPreSignedURL.html)
- [AWS Lambda Programming Model](http://docs.aws.amazon.com/lambda/latest/dg/programming-model.html)
- [AWS Lambda Limits](http://docs.aws.amazon.com/lambda/latest/dg/limits.html)
- [Node.js async programming](http://www.hacksparrow.com/node-js-async-programming.html)
- [AWS Lambda Example with Async Series](https://www.snip2code.com/Snippet/254166/AWS-Lambda-script---Compiling-JavaScript)
- [Python on AWS Lambda](http://willyg302.github.io/blog/posts/2015-03-29-python-on-aws-lambda/)
- [Javascript Microsevices on AWS](http://anders.janmyr.com/2014/12/lambda-javascript-micro-services-on-aws.html)
- [Material Design Light](http://www.getmdl.io/)
