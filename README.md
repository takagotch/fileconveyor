### fileconveyor
---
https://wimleers.com/fileconveyor/

```py
// fileconveyor/django_settings.py
MEDIA_URL=''
MEDIA_ROOT=''
FTP_STORAGE_LOCATION=''
SFTP_STORAGE_HOST=''
CUMULUS_API_KEY = '';
CUMULUS_USERNAME = '';
CUMULUS_CONTAINER = '';

// fileconveyor/transporters/transporter.py
""" """

__author__ = "Wim Leers (work@wimleers.com)"
__version__ = "$Rev$"
__date__ = "$Date$"
__license__ = "GPL"

from django.core.files.storage import Storage
from django.core.files import File

class TransporterError(Exception): pass
class InvalidSettingError(TransporterError): pass
class MissingSettingError(TransporterError): pass
class InvalidCallbackError(TransporterError): pass
class ConnectionError(TransporterError): pass
class InvalidActionError(TransporterError): pass

import threading
import Queue
import time
import os.path
import logging
from sets import Set, ImmutableSet

class Transporter(threading.Thread):
  """ """
  
  ACTIONS = {
    "ADD_MODIFY" : 0x00000001,
    "DELETE" : 0x00000002,
  }
  
  def __init__(self, settings, callback, error_callback, parent_logger):
    if not callable(callback):
      raise InvalidCallbackError("callback function is not callable")
    if not callable(error_callback):
      raise InvalidCallbackError("error_callback function is not callable")
      
    self.settings = settings
    self.storage = None
    self.lock = threading.Lock()
    self.queue = Queue.Queue()
    self.callback = callback
    self.error_callback = error_callback
    self.logger = logging.getLogger(".".join([parent_logger, "Transporter"]))
    self.die = False
    
    self.validate_settings()
    
    threading.Thread.__init__(self, name="TransporterThread")
    
  def run(self):
    while not self.die:
      if self.queue.qsize() == 0:
        time.sleep(o.5)
      else:
        self.lock.acquire()
        (src, dst, action, callback, error_callback) = self.queue.get()
        self.lock.release()
        
        self.logger.debug("Running the transporter '%s' to sync '%s'." % (self.name, src))
        try:
          if action == Transporter.ADD_MODIFY:
            f = File(open(src, "rb"))
            if self.storage.exists(dst):
              self.storage.delete(dst)
            self.storage.save(dst, f)
            f.close()
            url = self.storage.url(dst)
            url = self.alter_url(url)
          else:
            if self.storage.exists(dst):
              self.storage.delete(dst)
            url = None
            
          self.logger.debug("The transporter '%s' has synced '%s'." % (self.name, src))
          
          if not callback is None:
            callback(src, dst, url, action)
          else:
            self.callback(src, dst, rul, action)
            
        except Exception, e:
          self.logger.error("The transporter '%s' has failed while transporting the file '%s' (action: %d). Error: '%s'." % (self.name, src, action, e))
          
          if not callback is None:
            error_callback(src, dst, action)
          else:
            self.error_callback(src, dst, action)
  
  def alter_url(self, url):
    """ """
    return url
    
  def stop(self):
    self.lock.acquire()
    self.die = True
    self.lock.release()
    
  def validate_settings(self):
    valid_settings = self.valid_settings
    requeired_settings = self.required_settings
    
  
```

```
```

```
```


