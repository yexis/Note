##### 1 方向映射

```json
{
    "title": "方向映射",
    "rules": [
        {
            "description": "将左command+IJKL变成方向键",
            "manipulators": [
                {
                    "type": "basic",
                    "from": {
                        "key_code": "i",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "up_arrow"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "j",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "left_arrow"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "k",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "down_arrow"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "l",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "right_arrow"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "u",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "home"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "o",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "end"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "return_or_enter",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "return_or_enter"}]
                }
            ]
        }
    ]
}
```



##### 1.1 方向2

```json
{
    "title": "方向映射",
    "rules": [
        {
            "description": "将左command+IJKL变成方向键",
            "manipulators": [
                {
                    "type": "basic",
                    "from": {
                        "key_code": "i",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "up_arrow"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "j",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "left_arrow"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "k",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "down_arrow"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "l",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "right_arrow"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "u",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "home"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "o",
                        "modifiers": {
                            "mandatory": ["left_command"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "end"}]
                }
            ]
        }
    ]
}
```



##### 2 home end

```json
{
    "title": "home/end 映射",
    "rules": [
        {
            "description": "将左command+U/O 变成 home/end",
            "manipulators": [
                {
                    "type": "basic",
                    "from": {
                        "key_code": "u",
                        "modifiers": {
                            "mandatory": ["left_option"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "home"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "o",
                        "modifiers": {
                            "mandatory": ["left_option"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "end"}]
                }
            ]
        }
    ]
}


{
    "title": "home/end 映射",
    "rules": [
        {
            "description": "将 caps_lock + U/O 变成 home/end",
            "manipulators": [
                {
                    "type": "basic",
                    "from": {
                        "key_code": "u",
                        "modifiers": {
                            "mandatory": ["right_control"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "home"}]
                },
                {
                    "type": "basic",
                    "from": {
                        "key_code": "o",
                        "modifiers": {
                            "mandatory": ["right_control"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "end"}]
                }
            ]
        }
    ]
}
```



##### 3 delete

```json
{
    "title": "delete 映射",
    "rules": [
        {
            "description": "将左command+backspace 变成 home/end",
            "manipulators": [
                {
                    "type": "basic",
                    "from": {
                        "key_code": "backspace",
                        "modifiers": {
                            "mandatory": ["left_option"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "delete_forward"}]
                }
            ]
        }
    ]
}
```



##### 4 enter

```json
{
    "title": "enter 映射",
    "rules": [
        {
            "description": "将左 option+return_or_enter 变成 return_or_enter",
            "manipulators": [
                {
                    "type": "basic",
                    "from": {
                        "key_code": "return_or_enter",
                        "modifiers": {
                            "mandatory": ["left_option"],
                            "optional": ["any"]
                        }
                    },
                    "to": [{"key_code": "return_or_enter"}]
                }
            ]
        }
    ]
}
```

