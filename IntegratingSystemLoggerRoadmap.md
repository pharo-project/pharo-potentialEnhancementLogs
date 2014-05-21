This is version v1 21 May 2014 of the roadmap for SystemLogger

Have a  look at
  * http://smalltalkhub.com/#!/~StephaneDucasse/SystemLogger
  * and the chapter https://ci.inria.fr/pharo-contribution/job/PharoForTheEnterprise/lastSuccessfulBuild/artifact/Logger/


  * note that SystemLogger has a special logger that pushes strings to Transcript so we can have the same as now.

  *  right now in Object we have
        crTrace
        crTrace:
        trace
        trace:
        traceCr
        traceCr:
        logCr: deprecated
        crLog: deprecated
        so we can use trace: (I do not like it that much but this is ok)


  * check all the Transcript left reference
    * convert them to use the SystemLogger probably to trace: so that after we can simply rewrite trace:
    *  I think that it would be good to have a single entry point in Object (even at the cost to send another call to the main log recording method)

  * check all the log:
    * convert them (since a log is an entry we do not need to have explicit cr)

  *  may be use a circular structure for holding entries because right now the logger logs as much as possible.
    *  We should probably have different settings and the default should be circular

  *  produce a nice transcript with structured log entries
    *  log entries should be able to provide specific actions
    * for example a compiler logentry could hold a reference to the compiled method and we could ask to jump there
        - filter logentries

    - make sure that all the crLog: logCr: are nicely funnel to the systemLogger
        - and that everything works
