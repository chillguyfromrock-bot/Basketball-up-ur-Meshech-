```gpc
define Input_Interval = 25;

// Define button constants for cross-platform compatibility
define INTERACTION_MENU = PS5_TOUCH;

// Define variables for toggling features
int features_active = FALSE;

// Anti-recoil values for different guns (adjust these based on testing for each weapon type)define AR_RECOIL_VERT = 25; // Assault Rifle vertical recoil compensation
define SMG_RECOIL_VERT = 20; // SMG vertical recoil compensation
define SNIPER_RECOIL_VERT = 30; // Sniper vertical recoil compensation
define SHOTGUN_RECOIL_VERT = 15; // Shotgun vertical recoil compensation
define PISTOL_RECOIL_VERT = 18; // Pistol vertical recoil compensation

// Soft Aim and Sticky Aim settings
define SOFT_AIM_STRENGTH = 20; // Strength of soft aim (smooth tracking)define STICKY_AIM_STRENGTH = 30; // Strength of sticky aim (lock-on effect)int soft_aim_active = FALSE;
int sticky_aim_active = FALSE;

// Anti-recoil toggle for gun selection (default to AR)int current_gun = 0; // 0=AR, 1=SMG, 2=Sniper, 3=Shotgun, 4=Pistol

// Triple-click detection for left D-pad
int left_dpad_clicks = 0;
int last_click_time = 0;

init {
    // Swap button correctly for Playstation crossover on load
    if(((get_console() == PIO_PS5 || get_console() == PIO_PS4) && get_controller() != PIO_PS5 && get_controller() != PIO_PS4) ||
       (get_console() != PIO_PS5 && get_console() != PIO_PS4 && (get_controller() == PIO_PS5 || get_controller() == PIO_PS4))) {
        INTERACTION_MENU = PS5_TOUCH;}}main {
    // Triple-click detection for Left D-pad to toggle features
    if(event_press(PS5_LEFT)) {
        if(time_active(PS5_LEFT) - last_click_time < 500) {
            left_dpad_clicks++;} else {
            left_dpad_clicks = 1;} last_click_time = time_active(PS5_LEFT);        
        if(left_dpad_clicks >= 3) {
            features_active =!features_active;
            left_dpad_clicks = 0;
            if(features_active) {
                // Set LED to Green when features are active
                set_led(LED_1, 0); // Red off
                set_led(LED_2, 1); // Green on
                set_led(LED_3, 0); // Blue off} else {
                // Reset LED when features are off
                set_led(LED_1, 0);                set_led(LED_2, 0);                set_led(LED_3, 0);} }} // If features are active, run the following
    if(features_active) {
        // Reduce input delay for new-gen PS5 controller by minimizing wait times
        // (already handled by Input_Interval = 25ms)        
        // Soft Aim and Sticky Aim activation when L2 is interacted with
        if(get_val(PS5_L2)) {
            soft_aim_active = TRUE;
            sticky_aim_active = TRUE;
            combo_run(SoftAim);            combo_run(StickyAim);} else {
            soft_aim_active = FALSE;
            sticky_aim_active = FALSE;} // Anti-Recoil for different guns
        // Switch gun type with D-pad (example: Up=AR, Right=SMG, Down=Sniper, Left=Shotgun, L2+Left=Pistol)        if(get_val(PS5_UP) && event_press(PS5_UP)) {
            current_gun = 0; // AR} if(get_val(PS5_RIGHT) && event_press(PS5_RIGHT)) {
            current_gun = 1; // SMG} if(get_val(PS5_DOWN) && event_press(PS5_DOWN)) {
            current_gun = 2; // Sniper} if(get_val(PS5_LEFT) && event_press(PS5_LEFT)) {
            current_gun = 3; // Shotgun} if(get_val(PS5_L2) && event_press(PS5_LEFT)) {
            current_gun = 4; // Pistol} // Apply Anti-Recoil when shooting (R2)        if(get_val(PS5_R2)) {
            combo_run(AntiRecoil);} }}combo SoftAim {
    // Smooth soft aim tracking (adjusts right stick subtly towards target)    if(soft_aim_active) {
        set_val(PS5_RX, get_val(PS5_RX) + SOFT_AIM_STRENGTH);        set_val(PS5_RY, get_val(PS5_RY) + SOFT_AIM_STRENGTH);        wait(Input_Interval);        // Prevent crosshair flickering by limiting rapid adjustments
        set_val(PS5_RX, get_val(PS5_RX) - (SOFT_AIM_STRENGTH / 2));        set_val(PS5_RY, get_val(PS5_RY) - (SOFT_AIM_STRENGTH / 2));        wait(Input_Interval);}}combo StickyAim {
    // Strong sticky aim (locks onto target when L2 is held)    if(sticky_aim_active) {
        set_val(PS5_RX, get_val(PS5_RX) + STICKY_AIM_STRENGTH);        set_val(PS5_RY, get_val(PS5_RY) + STICKY_AIM_STRENGTH);        wait(Input_Interval);        // Smooth out movement to avoid camera shaking
        set_val(PS5_RX, get_val(PS5_RX) - (STICKY_AIM_STRENGTH / 3));        set_val(PS5_RY, get_val(PS5_RY) - (STICKY_AIM_STRENGTH / 3));        wait(Input_Interval);}}combo AntiRecoil {
    // Apply vertical recoil compensation based on selected gun
    if(get_val(PS5_R2)) {
        if(current_gun == 0) { // AR
            set_val(PS5_RY, get_val(PS5_RY) + AR_RECOIL_VERT);} else if(current_gun == 1) { // SMG
            set_val(PS5_RY, get_val(PS5_RY) + SMG_RECOIL_VERT);} else if(current_gun == 2) { // Sniper
            set_val(PS5_RY, get_val(PS5_RY) + SNIPER_RECOIL_VERT);} else if(current_gun == 3) { // Shotgun
            set_val(PS5_RY, get_val(PS5_RY) + SHOTGUN_RECOIL_VERT);} else if(current_gun == 4) { // Pistol
            set_val(PS5_RY, get_val(PS5_RY) + PISTOL_RECOIL_VERT);} wait(Input_Interval);}}```
