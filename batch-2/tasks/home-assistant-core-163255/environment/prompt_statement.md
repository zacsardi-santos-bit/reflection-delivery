I'm working on the Smarla baby rocker integration for Home Assistant (over in the `homeassistant/components/smarla` area) and I want to add firmware update support since right now there's just no way for folks to see what firmware version is installed, know when something newer is out, kick off an update, or watch it happen. It's all managed outside HA which is annoying.

So I need a new update entity wired into the integration, following the standard Home Assistant update platform stuff. It should surface the currently installed firmware version and the latest available version by polling/checking for updates periodically. When a newer version shows up, the entity needs to indicate an update's ready so the user can install it right from the normal HA update UI. Also while an update's downloading or installing I want the entity to reflect that in progress state in real time, not just flip at the end.

Oh and the failure case matters: if we can't figure out the update status (connectivity dies, the check fails, whatever) and there's no version info to go on, the entity should go to an unknown state instead of showing stale or wrong data. Basically don't lie about the version if we don't actually know it.

Point is to give users one unified place to keep the rocker's firmware current with proper feedback during the whole process.
