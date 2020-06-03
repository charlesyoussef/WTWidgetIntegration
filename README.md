## WTWidgetIntegration

# Embedding Cisco Collaboration Experience into Web Applications:

For embedding Cisco Webex Video Conferencing & collaboration experience in a Web application, the Cisco Webex Teams Widgets can be used.

Webex Teams Widgets can be included directly in your web applications, giving the ability to customize the Webex collaboration experience. They come pre-built with the Webex Teams User Interface (UI) or may be customized to your brand.

Webex Teams Widgets documentation:
https://developer.webex.com/docs/widgets

For demos or testing of Widgets, please check:
https://github.com/CiscoDevNet/webex-widget-samples

# Managing Guest Users:

For remote guest users who do not have Webex Teams accounts, the feature of "Guest Issuer" (AKA Persistent Guests) can be used to give the guest users temporary access to permanent users within your organization. For more information:
https://developer.webex.com/my-apps/new/guest-issuer

To more easily generate the guest temporary access tokens, the guestissuer CLI tool can be used:
https://github.com/ObjectIsAdvantag/webex-guestissuer

Steps to use the guest issuer:
1. Create a guest issuer app under your Webex profile at https://developer.webex.com
2. Make note of the 2 secret strings you get (issuer ID and shared secret), and make sure to store and use them in a very safe way
3. Install the guestissuer CLI tool as per the instructions on the github page above
4. Use the guestissuer CLI referencing the secret strings for your guest issuer app:
# guestissuer quick <guest-id> <guest-name> -i <issuer-ID> -s <shared-secret>

As an example:
cyoussef$ guestissuer quick John.Smith John Smith -i Y2lzY2....5YS01ZTAzLTQ -s y50mx....qs+mmZGcI=
Here is an access token, valid for: 1209599 seconds
ODAzZjAzZDktOTJhMy00YjY3LThkM...8-9e3a-06296664e4ed


# Using the Widget:

The identity of the Widget user is strictly defined by the Webex token passed to the <script> inside the HTML file. The token can be for a guest user (as obtained in the section above), or a permanent user (using an OAUTH integration app), or a bot token.
  
 The Widget can open either the "Recents" or the "Space" widgets, or a combination of both.
 
 The space widget can be for a one-to-one space (to a specific user or bot), or a group space. 
 
 The widget will then use the passed token and the user/group ID/email in order to make REST API calls to the Webex cloud.
 
 The collaboration options that the widget user has (calling, chatting, file sharing, etc) are also defined inside the Widget script.
 
 As an example:
 
1) Here is an example of a one-to-one space widget:

  <script src="https://code.s4d.io/widget-space/production/bundle.js"></script>
   <script>

      const widgetElement = document.getElementById('space');
      ciscospark.widget(widgetElement).spaceWidget({
         accessToken: '{{.Env.ACCESS_TOKEN}}', //user access token
         destinationType: "email",  //destination is a 1-1 space, defined by the user email address
         destinationId: '{{if .URL.Query.Get "email"}}{{.URL.Query.Get "email"}}{{else}}CiscoDevNet@webex.bot{{end}}', //user email address
         activities: { "files": true, "message": true, "people": true },
         initialActivity: 'message'
      });

   </script>
   
   
  2) Here is an example of a group widget:
  """
     <div style="width: 500px; height: 330px;" id="space" />

   <script src="https://code.s4d.io/widget-space/production/bundle.js"></script>
   <script>
  
      const widgetElement = document.getElementById('space');
      ciscospark.widget(widgetElement).spaceWidget({
		 accessToken: '{{.Env.ACCESS_TOKEN}}',  //widget user token
		 destinationId: 'Y2lzY29zcG......TBmYzY4OWM5',  //group space ID. It can be passed as an ENV variable as well
		destinationType: 'spaceId',  // this defined that a group space ID is passed
		spaceActivities: {"files":true,"meet":true,"message":true,"people":true},
		initialActivity: 'message',
		secondaryActivitiesFullWidth: false
      });

   </script>
"""
