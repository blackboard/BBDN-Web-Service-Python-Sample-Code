# BBDN-Web-Service-Python-Sample-Code
This project contains sample code for interacting with the Blackboard Learn SOAP Web Services in python.

Created on Feb 12, 2015

@author: shurrey

This sample code was built with Python 2.7.9.

You will need to install SUDS. I am using a branch of SUDS that is maintained (the original SUDS project has gone stagnant).

You can download this library from here:
  https://bitbucket.org/jurko/suds

This is a third-party library not associated with Blackboard in any way. Use at your own risk.

Also, this software is set up to use the Learn Developer Virtual Machine. The virtual machine contains a self-signed certificate,
which will cause Python's urllib2 module to fail. Because the Blackboard Learn 9.1 April and newer releases require you to use SSL,
you must make a change to Python's urllib2 module manually. THIS CHANGE WILL BYPASS SSL CERTIFICATE CHECKING, so be sure to undo 
this change when rolling out to production.

To make this change, find the library urllib2. You can find it in the directory you installed python. For me it is:
    .../python/2.7.9/Frameworks/Python.framework/Versions/2.7/lib/python2.7/urllib2.py

Edit this file, and search for the class HTTPHandler. It will look like this:

        class HTTPHandler(AbstractHTTPHandler):
        
            def http_open(self, req):
                return self.do_open(httplib.HTTPConnection, req)
        
            http_request = AbstractHTTPHandler.do_request_
        
        if hasattr(httplib, 'HTTPS'):
            class HTTPSHandler(AbstractHTTPHandler):
        
                def __init__(self, debuglevel=0, context=None):
                    AbstractHTTPHandler.__init__(self, debuglevel)
                    self._context = context
        
                def https_open(self, req):
                    return self.do_open(httplib.HTTPSConnection, req,
                        context=self._context)
        
                https_request = AbstractHTTPHandler.do_request_
        
Make it look like this:

        class HTTPHandler(AbstractHTTPHandler):
        
            def http_open(self, req):
                return self.do_open(httplib.HTTPConnection, req)
        
            http_request = AbstractHTTPHandler.do_request_
        
        if hasattr(httplib, 'HTTPS'):
            class HTTPSHandler(AbstractHTTPHandler):
        
                def __init__(self, debuglevel=0, context=None):
                    gcontext = ssl.SSLContext(ssl.PROTOCOL_TLSv1)   # Only for gangstars
                    AbstractHTTPHandler.__init__(self, debuglevel)
                    self._context = gcontext                        # Change context to gcontext
        
                def https_open(self, req):
                    return self.do_open(httplib.HTTPSConnection, req,
                        context=self._context)
        
                https_request = AbstractHTTPHandler.do_request_
