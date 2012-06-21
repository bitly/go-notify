Package notify enables independent components of an application to 
observe notable events in a decoupled fashion.

It generalizes the pattern of *multiple* consumers of an event (ie: 
the same message delivered to multiple channels) and obviates the need 
for components to have intimate knowledge of each other (only `import notify` 
and the name of the event are shared).

Example:

    // producer of "my_event" 
    for {
        select {
        case <-time.Tick(time.Duration(1) * time.Second):
            notify.Post("my_event", time.Now().Unix())
        }
    }
    
    // observer of "my_event" (normally some independent component that
    // needs to be notified)
    myEventChan := make(chan interface{})
    notify.Observe("my_event", myEventChan)
    go func() {
        for {
            data := <-myEventChan
            log.Printf("MY_EVENT: %#v", data)
        }
    }()
