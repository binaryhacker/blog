+++
date = "2017-06-18T18:41:47+05:30"
draft = false
title = "Android HAL (Hardware Abstraction Layer)"
tags = ["android" , "HAL" , "embedded" ]
+++

Hardare Abstraction Layer is the software component which abstracts the hardware . In one context  `operating system` is a hardware abstraction layer . In another context we can say a pheripheral driver is a hardware abstraction layer for that pheripheral hardware . Or some time we can use userspace drievers  which does the job of abstracting the hardware . 

Hardware Abstraction Layer in the context of android operating system is a binary blob which will talk to hardware . `HAL` is nothing more than shared `elf` object ! . Most of the hardware pheripherals will have a `HAL` associated with it. For example wifi , camera , display etc.

In normal embedded linux scenario we will talk to linux kernel via system calls, And system calls are kind of standerd interface for the user space programs to talk to the kernel. In android this is not the case , All applications will try to comply with hal layer instead of the system call layer. 

Every HAL module have a well known symbol called `HMI`. Any subusystem / service request a HAL via a function called `hw_get_module()` . hw\_get\_module. This function can be found in `hardware/libhardware` . 

## hardware_module_t structure

Every hardware module must have a data structure named `HAL_MODULE_INFO_SYM` and the fields of this data structure must begin with `hw_module_t` followed by module specific information. 

```c
typedef struct hw_module_t {
	uint32_t tag;
	uint16_t module_api_version;
	uint16_t hal_api_version;
	const char *id;
	const char *name;
	const char *author;
	struct hw_module_methods_t* methods;
	void* dso;
#ifdef __LP64__
	uint64_t reserved[32-7];
#else
	uint32_t reserved[32-7];
#endif
} hw_module_t;
```

The tag field defines it is hardware module , the 32 bit integer distingueshes the structure from hardware module and hardware device . See the below code snippet to figure out how the tag field is constructed . 

```c
#define MAKE_TAG_CONSTANT(A,B,C,D) (((A) << 24) | ((B) << 16) | ((C) << 8) | (D))
#define HARDWARE_MODULE_TAG MAKE_TAG_CONSTANT('H', 'W', 'M', 'T')
#define HARDWARE_DEVICE_TAG MAKE_TAG_CONSTANT('H', 'W', 'D', 'T')
```

The module_api_version field defines the version of the api used for that specific module . The hal_api_version field defines the api version of the hal module itself .  Each hardware module is identified by an id , the `id` field is used to define the unique identifier of the hardware module. The methords field is used to open instanciate the hardware_device_t structure . 

```c
#define CAMERA_HARDWARE_MODULE_ID "camera"
#define SENSORS_HARDWARE_MODULE_ID "sensors"
#define FINGERPRINT_HARDWARE_MODULE_ID "fingerprint"
#define NFC_NCI_HARDWARE_MODULE_ID "nfc_nci"
#define GPS_HARDWARE_MODULE_ID "gps"
...
```

The name field defines the the neme of the device. The author field can be used for registering the module authors name . 

```c
typedef struct hw_module_methods_t {
/** Open a specific device */
int (*open)(const struct hw_module_t* module, const char* id,
struct hw_device_t** device);
} hw_module_methods_t;
```
## hw_device_t structure

```c
typedef struct hw_device_t {
	uint32_t tag;
	uint32_t version;
	struct hw_module_t* module;
#ifdef __LP64__
	uint64_t reserved[12];
#else
	uint32_t reserved[12];
#endif
	int (*close)(struct hw_device_t* device);
} hw_device_t;
```

Like hardware_module_t in hw_device_t structure also there is a tag field this field defines it as a hardware device . `version` field can be used for defineing the version of the hardware device . The module field can be used to store hardware module instance . The close field can be used to free the device memory instance .   



Now lets see some exaple implementations .

## Fingerprint HAL

In the fingerprint hal as every other hal module has a well known symbol "HMI" . HAL_MODULE_INFO_SYM is a macro which expands to HMI . 

```c
/**
* Name of the hal_module_info
*/
#define HAL_MODULE_INFO_SYM         HMI

/**
* Name of the hal_module_info as a string
*/
#define HAL_MODULE_INFO_SYM_AS_STR  "HMI"
```

This symbol "HMI" sould have hw_module_t structure at the starting address . So every hal module will have a structure for its own fields but for sure the first field of the structure will be hw_module_t structure . In this case fingerprint hal module we are using finger_print_module_t . see below for the defanition for `fingerprint_module_t`

```c
typedef struct fingerprint_module {
/**
* Common methods of the fingerprint module. This *must* be the first member
* of fingerprint_module as users of this structure will cast a hw_module_t
* to fingerprint_module pointer in contexts where it's known 
* the hw_module_t references a fingerprint_module.
*/ 
	struct hw_module_t common;
} fingerprint_module_t;
```

```c
fingerprint_module_t HAL_MODULE_INFO_SYM = {
	.common = {
		.tag                = HARDWARE_MODULE_TAG,
		.module_api_version = FINGERPRINT_MODULE_API_VERSION_2_0,
		.hal_api_version    = HARDWARE_HAL_API_VERSION,
		.id                 = FINGERPRINT_HARDWARE_MODULE_ID,
		.name               = "Demo Fingerprint HAL",
		.author             = "The Android Open Source Project",
		.methods            = &fingerprint_module_methods,
	},
};
```
All the structure feilds are intiaizlized as we have discussed eariler . The Main fields is methords field which opens and instainsiate an Fingerprint device . Lets see how its implemented in a demo hal .

```c
static int fingerprint_open(const hw_module_t* module, const char __unused *id,
hw_device_t** device)
{
	if (device == NULL) {
		ALOGE("NULL device on open");
		return -EINVAL;
	}

	fingerprint_device_t *dev = malloc(sizeof(fingerprint_device_t));
	memset(dev, 0, sizeof(fingerprint_device_t));

	dev->common.tag = HARDWARE_DEVICE_TAG;
	dev->common.version = FINGERPRINT_MODULE_API_VERSION_2_0;
	dev->common.module = (struct hw_module_t*) module;
	dev->common.close = fingerprint_close;

	dev->pre_enroll = fingerprint_pre_enroll;
	dev->enroll = fingerprint_enroll;
	dev->get_authenticator_id = fingerprint_get_auth_id;
	dev->cancel = fingerprint_cancel;
	dev->remove = fingerprint_remove;
	dev->set_active_group = fingerprint_set_active_group;
	dev->authenticate = fingerprint_authenticate;
	dev->set_notify = set_notify_callback;
	dev->notify = NULL;

	*device = (hw_device_t*) dev;
	return 0;
}

static struct hw_module_methods_t fingerprint_module_methods = {
	.open = fingerprint_open,
};
```

in the open methord we can see that fingerprint hal initizes the fingerprint device and defines the necessary function pointers for further usage . In the next post we can see how the halmodules are loaded 
