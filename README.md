# CK3ACS
Project for Advance Character Search mode for Crusader Kings 3

=====================
Initialisation of mod
=====================
Trying to sort out what appears to be the incorrect initialisation of the mod which is highlighted by the error messages when a new game is started.

In acs_sg_init the global variable acs_first_time_setup_v9 is used to select the branch of a IF/ELSE but is never set anywhere so the ELSE will always be selected, but the
outer IF block uses acs_first_time_setup_v9_2 which is set after the IF block completes so the IF block is never entered again.

Neither acs_first_time_setup_v9 or acs_first_time_setup_v9_2 are used anywhere else.

So the inner IF/ELSE has to set acs_first_time_setup_v9 after the ELSE completes to make this truly a one time only initialisation which I think is your intention?

However that makes no sense to do that, because you are enforcing one time initialisation by both acs_first_time_setup_v9_2 and acs_first_time_setup_v9, but the outer IF block does
nothing except contain the inner IF/ELSE block. So it makes more sense to remove the inner IF block so it's function becomes the outer IF block's function and the inner ELSE block is
then attached to the outer IF block, so that way acs_first_time_setup_v9_2 controls the one time initialisation, and acs_first_time_setup_v9 can be removed.

It appears the incorrect initialisation is caused by acs_sg_init being called in the acs_button_character_search_large state triggered by trigger_on_create, but the global variable
acs_gv_main_filters is used in create_searched_character_list via the acs_window sgui that is used in acs_window state triggered by trigger_on_create before the acs_button_character_search_large
is created.

So it makes sense to move acs_sg_init from the acs_button_character_search_large on_create state to the acs_window on_create state. Which also suggests the code to set mod_acs_active should
also be moved from acs_button_character_search_large to acs_window.
