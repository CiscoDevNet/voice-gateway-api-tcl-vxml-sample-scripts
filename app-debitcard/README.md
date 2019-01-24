The debitcard application allows a user to to select the lanaguage mode 
based on the langauges that are configured through the CLIs. The application 
then prompts and collects the card number. The card number consists of a uid 
and pin where uid-length and pin-length are configured through through CLIs.  
Authentication is done with the card number. If it passes authentication,
the application plays the amount available in the user's debitcard. It then
prompts and collects the destination number.
