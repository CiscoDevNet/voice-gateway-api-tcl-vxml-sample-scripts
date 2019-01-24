Functionality:

   Script supports h450 call transfer.
                   
   It is a universal script serving as role of transfer-or, transfer-to and transfer-ee.

Basic configuration:

    call application voice appname tftp://.../app-h450-transfer.2.0.1.0.tcl
    call application voice appname language 1 en
    call application voice appname set-location en 0 tftp://.../prompts/en/

    Note: One language configuration is mandatory.

User interface for analog phone user acting as transfer-or:

    Analog phone user can act as transfer-or by using hookflash to initiate consultation call.

    To initiate a call transfer, transfer-or pushes hookflash,the transfer-ee is immediately music-on-hold, 
    transfer-or hears a dial-tone prompting for destination collection to transfer-to. 

    Here is the list of scenarios after transfer-or enters the destination for consultation call:

    1. Transfer-or hangs up the phone before hearing ringing, destination phone rings, music-on-hold party will hear ringing.
       Transfer-to picks up the ringing phone, transfer-to and transfer-ee are in talk. 

    2. Transfer-or hangs up the phone after hearing ringing, transfer-ee hears ringing instead of music.
       Transfer-to picks up the ringing phone, transfer-to and transfer-ee are in talk. 

    3. Transfer-or hears ringing, transfer-to picks up the ringing phone, transfer-or and transfer-to are in talk. 

       At this point, transfer-or can: 

         a. Hangs up the phone to commit call transfer.

         b. Use hookflash to toggle between two active calls and then hangs up to commit call transfer.

      After transfer-or commits transfer, transfer-to and transfer-ee are in talk. 
       
CLI parameters (AV pairs) accessible by the script:

    1. delay-time 

       It is used to configure the delay time for consultation call setup when analog phone is being used. Default 
       delay-time is 2 seconds. 

       eg. call application voice appname delay-time 1

    2. max-fwd-cnt 

       This parameter is the maximum number of  rotary call setup allowed by the script while a call is being forwarded.
       The range is 1-7, default is 5.

       eg. call application voice appname max-fwd-cnt 6

    3. announce-for-busy

       If this parameter is configured, if call setup fails with status code user busy(ls_007), script will connect the 
       incoming leg and play announcement en_operator-is-busy.au and then closes call.

       Can only be configured as value 1. 

       eg. call application voice appname announce-for-busy 1

     4. disc-prog-ind-at-connect

       With this parameter is configured, if script receives ev_disc_prog_ind while call is active, script will treat it
       same as ev_disconnected.

       Can only be configured as value 1.

       eg. call application voice appname disc-prog-ind-at-connect 1

    5. local-hairpin

       If this parameter is configured, script will attempt rotary instead of redirect_rotary during call transfer.

       For all cases of call transfer initiated by Vespa phone, script will receive ev_transfer_request, 
       script will retrieve the transfer-to destination and use it to setup a rotary call towards transfer-to destination.

       For pure blind transfer initiated by FXS phone, script will directly setup a rotary call to the destination of transfer-to.

       For transfer at alert initiated by FXS phone, script plays ring-back to transfer-ee and bridge transfer-ee and transfer-to 
       after trnasfer-to picks up.

       For consultation transfer initiated by FXS phone, the script simply bridge the call legs of transfer-ee and transfer-to.

       This parameter can only be configured as value 1.

       eg. call application voice appname local-hairpin 1

    6. codec

       This parameter is used to configured the type audio prompts for script to use. 
       
       By default script uses ulaw audio files which is g711ulaw. Value can only be alaw and ulaw.

       eg. call application voice appname codec alaw

    7. disable-early-media

       If this parameter is configured, script will diable early media by propagating alert instead of progress towards incoming
       SIP voip leg.
       
       Can only be configured as value 1.

       eg. call application voice appname disable-early-media 1

    8. anonymous-prefix

       If this parameter is configured, if destination has this prefix numer, script will replace SIP header field "From" to 
       "sip:anonymous@localhost".

       Can only be configured as value 1.

       eg. call application voice appname anonymous-prefix 1234

       NOTE: When no sip-remote-party-domain-name configured

             - remote party will see "anonymous" as calling party

             - remote party will see "Private" as calling party when "no remote-party-id" is configured

    9. sip-remote-party-domain-name

       If this parameter is configured, script will replace Remote-Party-ID field in SIP header with 
       "<sip:AAAA@BBBB>;party=calling;screen=no;privacy=full" where AAAA is ANI and BBBB is the configured value of 
       sip-remote-party-domain-name.

       eg. call application voice appname sip-remote-party-domain-name www.cisco.com

       NOTE: This configuration is only valid when anonymous-prefix is configured with "1".


    10.sip-contact-address

       If this parameter is configured, script will replace Contact field in SIP header with 
       "<sip:AAAA@BBBB>"

       eg. call application voice appname sip-contact-address abc@www.cisco.com

       NOTE: This configuration is only valid when anonymous-prefix is configured with "1".
       

IOS version requirement:

    IOS version requirement:

    12.2(15)ZJ1 and after

