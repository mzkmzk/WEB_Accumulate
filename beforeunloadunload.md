# beforeunload和unload



window.addEventListener('beforeunload', function(e){
  var msg = "Do u want to leave?\nChanges u made may be lost."
  //var evt = window.event
  e.returnValue = msg
})