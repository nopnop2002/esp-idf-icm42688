idf_component_register(
	SRCS "ICM42688.cpp"
	PRIV_REQUIRES driver esp_driver_i2c esp_timer
	INCLUDE_DIRS "."
)
