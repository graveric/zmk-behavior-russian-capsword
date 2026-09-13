cmake_minimum_required(VERSION 3.20.0)

if ((NOT CONFIG_ZMK_SPLIT) OR CONFIG_ZMK_SPLIT_ROLE_CENTRAL)
  target_sources_ifdef(CONFIG_ZMK_BEHAVIOR_RUSSIAN_CAPSWORD
      app PRIVATE src/behaviors/behavior_russian_capsword.c
  )
endif()