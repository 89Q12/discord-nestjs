# Common module

## 🧾 Description

This package contains some ready-made templates

## 👨🏻‍💻 Installation <a name="Installation"></a>

```bash
$ npm install @discord-nestjs/common
```

Or via yarn

```bash
$ yarn add @discord-nestjs/common
```

## 📑 Overview <a name="Overview"></a>

### ℹ️ Pipes template

`TransformPipe` fills in the fields `DTO` from `CommandInteraction`.

#### 💡 Example

```typescript
/* registration-email.dto.ts */

import { City } from '../definitions/city';
import { Param, ParamType, Choice } from '@discord-nestjs/core';

export class RegistrationEmailDto {
  @Param({
    name: 'email',
    description: 'Base user email',
    required: true,
  })
  email: string;

  @Param({ description: 'User nickname', required: true })
  name: string;

  @Param({ description: 'User age', required: true, type: ParamType.INTEGER })
  age: number;

  @Choice(City)
  @Param({ description: 'City of residence', type: ParamType.INTEGER })
  city: City;
}
```

```typescript
/* registration.command.ts */

import { RegistrationDto } from './registration.dto';
import { Command, UsePipes, Payload } from '@discord-nestjs/core';
import { TransformPipe } from '@discord-nestjs/common';
import { DiscordTransformedCommand } from '@discord-nestjs/core/src';
import { CommandInteraction } from 'discord.js';

@Command({
  name: 'reg',
  description: 'User registration',
})
@UsePipes(TransformPipe)
export class BaseInfoCommand implements DiscordTransformedCommand<RegistrationEmailDto>
{
  handler(@Payload() dto: RegistrationEmailDto): string {
    // dto instance must have the following fields: email, name, age, city
  }
}
```

`ValidationPipe` validate the resulting `DTO` based on class-validator. If the `DTO` is invalid then an exception will be thrown, 
which can be caught by the filter from the package `@discord-nestjs/core`.

> for validation, you need to install package `class-validator`

#### 💡 Example

```typescript
/* registration-email.dto.ts */

import { Param, ParamType } from '@discord-nestjs/core';
import { IsEmail, Length, Max, Min } from 'class-validator';

export class RegistrationEmailDto {
  @IsEmail()
  @Param({
    name: 'email',
    description: 'Base user email',
    required: true,
  })
  email: string;

  @Param({ description: 'User nickname', required: true })
  @Length(3, 100)
  name: string;

  @Param({ description: 'User age', required: true, type: ParamType.INTEGER })
  @Max(150)
  @Min(18)
  age: number;
}
```

```typescript
/* registration.command.ts */

import { RegistrationDto } from './registration.dto';
import { Command, UsePipes, Payload, DiscordTransformedCommand } from '@discord-nestjs/core';
import { TransformPipe, ValidationPipe } from '@discord-nestjs/common';
import { CommandInteraction } from 'discord.js';

@Command({
  name: 'reg',
  description: 'User registration',
})
@UsePipes(TransformPipe, ValidationPipe)
export class BaseInfoCommand implements DiscordTransformedCommand<RegistrationEmailDto> {
  handler(@Payload() dto: RegistrationEmailDto): string {
    // dto instance must be valid
  }
}
```
