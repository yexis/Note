##### hyper

```json
{
    "title": "hyper", 
    "rules": [
        {
            "description": "hyper", 
            "manipulators": [
                {
                    "type": "basic", 
                    "from": {
                        "key_code": "caps_lock", 
                        "modifiers": {
                            "optional": [
                                "any"
                            ]
                        }
                    }, 
                    "to": [
                        {
                            "key_code": "right_shift", 
                            "modifiers": [
                                "right_command", 
                                "right_control", 
                                "right_option"
                            ]
                        }
                    ]
                }
            ]
        }
    ]
}

```



##### all

```json
{
    "title": "all", 
    "rules": [
        {
            "description": "all", 
            "manipulators": [
                {
                    "type": "basic", 
                    "from": {
                        "key_code": "i", 
                        "modifiers": {
                            "mandatory": [
                                "left_command"
                            ], 
                            "optional": [
                                "any"
                            ]
                        }
                    }, 
                    "to": [
                        {
                            "key_code": "up_arrow"
                        }
                    ]
                }, 
                {
                    "type": "basic", 
                    "from": {
                        "key_code": "j", 
                        "modifiers": {
                            "mandatory": [
                                "left_command"
                            ], 
                            "optional": [
                                "any"
                            ]
                        }
                    }, 
                    "to": [
                        {
                            "key_code": "left_arrow"
                        }
                    ]
                }, 
                {
                    "type": "basic", 
                    "from": {
                        "key_code": "k", 
                        "modifiers": {
                            "mandatory": [
                                "left_command"
                            ], 
                            "optional": [
                                "any"
                            ]
                        }
                    }, 
                    "to": [
                        {
                            "key_code": "down_arrow"
                        }
                    ]
                }, 
                {
                    "type": "basic", 
                    "from": {
                        "key_code": "l", 
                        "modifiers": {
                            "mandatory": [
                                "left_command"
                            ], 
                            "optional": [
                                "any"
                            ]
                        }
                    }, 
                    "to": [
                        {
                            "key_code": "right_arrow"
                        }
                    ]
                }, 
                {
                    "type": "basic", 
                    "from": {
                        "key_code": "u", 
                        "modifiers": {
                            "mandatory": [
                                "left_command"
                            ], 
                            "optional": [
                                "any"
                            ]
                        }
                    }, 
                    "to": [
                        {
                            "key_code": "a", 
                            "modifiers": [
                                "left_control"
                            ]
                        }
                    ]
                }, 
                {
                    "type": "basic", 
                    "from": {
                        "key_code": "o", 
                        "modifiers": {
                            "mandatory": [
                                "left_command"
                            ], 
                            "optional": [
                                "any"
                            ]
                        }
                    }, 
                    "to": [
                        {
                            "key_code": "e", 
                            "modifiers": [
                                "left_control"
                            ]
                        }
                    ]
                }, 
                {
                    "type": "basic", 
                    "from": {
                        "key_code": "return_or_enter", 
                        "modifiers": {
                            "mandatory": [
                                "left_command"
                            ], 
                            "optional": [
                                "any"
                            ]
                        }
                    }, 
                    "to": [
                        {
                            "key_code": "e", 
                            "modifiers": [
                                "right_control"
                            ]
                        }, 
                        {
                            "key_code": "return_or_enter"
                        }
                    ]
                }, 
                {
                    "type": "basic", 
                    "from": {
                        "key_code": "u", 
                        "modifiers": {
                            "mandatory": [
                                "right_command", 
                                "right_control", 
                                "right_option", 
                                "right_shift"
                            ], 
                            "optional": [
                                "any"
                            ]
                        }
                    }, 
                    "to": [
                        {
                            "key_code": "left_arrow", 
                            "modifiers": [
                                "left_command", 
                                "left_shift"
                            ]
                        }
                    ]
                }, 
                {
                    "type": "basic", 
                    "from": {
                        "key_code": "o", 
                        "modifiers": {
                            "mandatory": [
                                "right_command", 
                                "right_control", 
                                "right_option", 
                                "right_shift"
                            ], 
                            "optional": [
                                "any"
                            ]
                        }
                    }, 
                    "to": [
                        {
                            "key_code": "right_arrow", 
                            "modifiers": [
                                "left_shift", 
                                "left_command"
                            ]
                        }
                    ]
                }, 
                {
                    "type": "basic", 
                    "from": {
                        "key_code": "y", 
                        "modifiers": {
                            "mandatory": [
                                "left_command"
                            ], 
                            "optional": [
                                "any"
                            ]
                        }
                    }, 
                    "to": [
                        {
                            "key_code": "left_arrow", 
                            "modifiers": [
                                "left_command", 
                                "left_shift"
                            ]
                        }
                    ]
                }, 
                {
                    "type": "basic", 
                    "from": {
                        "key_code": "p", 
                        "modifiers": {
                            "mandatory": [
                                "left_command"
                            ], 
                            "optional": [
                                "any"
                            ]
                        }
                    }, 
                    "to": [
                        {
                            "key_code": "right_arrow", 
                            "modifiers": [
                                "left_shift", 
                                "left_command"
                            ]
                        }
                    ]
                }
            ]
        }
    ]
}
```

