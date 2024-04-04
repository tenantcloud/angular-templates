## TenantCloud Angular Testing Live Templates for PhpStorm ##

How to:

1) Go to *Preferences | Tools | Settings Repository*

2) Add Read-only Source https://github.com/tenantcloud/angular-templates.git

3) Restart PhpStorm.

4) To see all templates, go to *Preferences | Live Templates* and expand the Template Group.


## Supported Live Templates ##

- [Mock](#mock)
- [PO](#po)
- [BeforeEach inject](#beforeeach-inject)
- [Service](#service)
- [API](#api)
- [Action Service](#action-service)
- [Component](#component)
- [Host component](#host-component)
- [Directive](#directive)
- [Pipe](#pipe)

### Mock ###
#### (mock + Tab) ####

```
import { faker } from '@faker-js/faker';

import { DataMock } from '@app/test-module/mocks/data/data.mock';

import { $NAME$ } from '../interfaces/$PATH$.interface';

export class $NAME$Mock extends DataMock implements $NAME$ { 

	constructor(data?: Partial<$NAME$>) {
		super();

		this.updateSelf(data);
	}
}
```

### PO ###
#### (po + Tab) ####

```
import { Spectator } from '@ngneat/spectator';

export class $NAME$PO {
	constructor(private readonly spectator: Spectator<unknown>) {}

	public get some(): HTMLElement {
		return this.spectator.query('.some') as HTMLElement;
	}
}
```

#### Host PO (poh + Tab) ####
```
import { SpectatorHost } from '@ngneat/spectator';

export class $NAME$PO {
	constructor(private readonly spectator: SpectatorHost<unknown>) {}

	public get some(): HTMLElement {
		return this.spectator.query('.some') as HTMLElement;
	}
}
```

#### PO getter (po:get + Tab) ####
```
public get some(): HTMLElement {
	return this.spectator.query('.some') as HTMLElement;
}
```


#### PO component getter (po:getc + Tab) ####
```
public get some(): $NAME$ {
	return this.spectator.query($NAME$) as $NAME$;
}
```

### BeforeEach inject ###
#### (bfei + Tab) ####

```
beforeEach(inject([$NAME$], ($LNAME$: $NAME$) => {
	
}));
```

### Service ###
#### (ss + Tab) ####
```
import { createServiceFactory, SpectatorService } from '@ngneat/spectator';

import { $NAME$ } from './$PATH$';

describe('$NAME$', () => {
	let service: $NAME$;
	let spectator: SpectatorService<$NAME$>;
	const createService = createServiceFactory({
		service: $NAME$,
	});

	beforeEach(() => {
		spectator = createService();
		service = spectator.service;
	});

	it('should create', () => {
		expect(service).toBeTruthy();
	});
});
```

### API ###
#### (sapi + Tab) ####

```
import { createHttpFactory, SpectatorHttp } from '@ngneat/spectator';
import { $NAME$ } from './$PATH$';

describe('$NAME$', () => {
	let service: $NAME$;
	let spectator: SpectatorHttp<$NAME$>;
	const createHttp = createHttpFactory({
		service: $NAME$,
	});

	beforeEach(() => {
		spectator = createHttp();
		service = spectator.service;
	});

	it('should create', () => {
		expect(service).toBeTruthy();
	});
});
```

#### API GET Test (sapi:get + Tab) ####

```
it('should get data', async () => {
	const data = {};
	const reqPromise = service.getData();

	spectator.expectOne('/some', HttpMethod.GET).flush(data);

	const resp = await reqPromise;

	expect(resp).toEqual(data);
});

```
#### API GET List Test (sapi:getlist + Tab) ####
```
it('check get list', async () => {
	const data = new ListItemsMock(Mock);
	const reqPromise = service.getList().toPromise();

	spectator.controller.expectOne(req => req.url.startsWith('/some') && req.method === HttpMethod.GET).flush(data);

	const resp = await reqPromise;

	expect(resp).toEqual(data);
});
```

### Action Service ###
#### (sas + Tab) ####

```
import { SpectatorService, createServiceFactory } from '@ngneat/spectator';

import { ModalService } from '@app/shared/modules/modal/services';
import { NotifyService } from '@app/core/modules/notify/services';
import { $API$ } from '../../api';
import { $NAME$ } from './$PATH$';

describe('$NAME$', () => {
	let service: $NAME$;
	let spectator: SpectatorService<$NAME$>;
	const createService = createServiceFactory({
		service: $NAME$,
	});

	let modalService: ModalService;
	let notifyService: NotifyService;
	let $API_V$: $API$;

	beforeEach(() => {
		spectator = createService();
		service = spectator.service;

		modalService = spectator.inject(ModalService);
		notifyService = spectator.inject(NotifyService);
		$API_V$ = spectator.inject($API$);
	});

	beforeEach(() => {
		spyOn(notifyService, 'success');
	});

	it('should create', () => {
		expect(service).toBeTruthy();
	});
});

```

#### Action Service Confirm Test (sas:confirm + Tab) ####

```
describe('$METHOD_NAME$', () => {
	const data = new Mock();

	beforeEach(() => {
		spyOn($API_V$, '$METHOD_NAME$').and.resolveTo();
	});

	it('should be success when confirmed', async () => {
		spyOn(modalService, 'confirm').and.resolveTo(true);

		const result = await service.$METHOD_NAME$(data);

		expect(result).toBeTrue();
		expect(modalService.confirm).toHaveBeenCalledWith(jasmine.any(Object));
		expect($API_V$.$METHOD_NAME$).toHaveBeenCalledWith(data);
		expect(notifyService.success).toHaveBeenCalledWith(jasmine.any(String));
	});

	it('should be return false when not confirmed', async () => {
		spyOn(modalService, 'confirm').and.resolveTo(false);

		const result = await service.$METHOD_NAME$(data);

		expect(result).toBeFalse();
		expect(modalService.confirm).toHaveBeenCalledWith(jasmine.any(Object));
		expect($API_V$.$METHOD_NAME$).not.toHaveBeenCalled();
		expect(notifyService.success).not.toHaveBeenCalled();
	});
});
```

#### Action Service Modal Test (sas:modal + Tab) ####

```
describe('$METHOD_NAME$', () => {
	const data = new Mock();

	it('should be successful and notify', async () => {
		const modalRef = jasmine.createSpyObj<ModalRef>([], { afterClosed$: of(true) });
		spyOn(modalService, 'open').and.returnValue(modalRef);

		const resp = await service.$METHOD_NAME$(data);

		expect(resp).toEqual(true);
		expect(modalService.open).toHaveBeenCalledWith(Component, { data });
		expect(notifyService.success).toHaveBeenCalledOnceWith(jasmine.any(String));
	});

	it('should handle modal close', async () => {
		const modalRef = jasmine.createSpyObj<ModalRef>([], { afterClosed$: of(null) });
		spyOn(modalService, 'open').and.returnValue(modalRef);

		const resp = await service.$METHOD_NAME$(data);

		expect(resp).toBeNull();
		expect(modalService.open).toHaveBeenCalledWith(Component, { data });
		expect(notifyService.success).not.toHaveBeenCalled();
	});
});
```

### Component ###
#### (sc + Tab) ####
```
import { createComponentFactory, Spectator } from '@ngneat/spectator';

import { $NAME$ } from './$PATH$';
import { $NAME$PO } from './$PATH$.po';

describe('$NAME$', () => {
	let spectator: Spectator<$NAME$>;
	let componentPO: $NAME$PO;
	const createComponent = createComponentFactory({
		component: $NAME$,
	});

	beforeEach(() => {
		spectator = createComponent();
		componentPO = new $NAME$PO(spectator);
	});

	it('should create', () => {
		expect(spectator.component).toBeTruthy();
	});
});
```

### Host Component ###
#### (sch + Tab) ####
```
import { createHostFactory, SpectatorHost } from '@ngneat/spectator';

import { $NAME$ } from './$PATH$';
import { $NAME$PO } from './$PATH$.po';

describe('$NAME$', () => {
	let spectator: SpectatorHost<$NAME$>;
	let componentPO: $NAME$PO;
	const createHost = createHostFactory({
		template: `<$SELECTOR$ />`
		component: $NAME$,
	});

	beforeEach(() => {
		spectator = createHost('');
		componentPO = new $NAME$PO(spectator);
	});

	it('should create', () => {
		expect(spectator.component).toBeTruthy();
	});
});
```

### Directive ###
#### (sd + Tab) ####
```
import { createDirectiveFactory, SpectatorDirective } from '@ngneat/spectator';

import { $NAME$ } from './$PATH$';

describe('$NAME$', () => {
	let spectator: SpectatorDirective<$NAME$>;
	const createDirective = createDirectiveFactory({
		directive: $NAME$,
	});

	beforeEach(() => {
		spectator = createDirective(`<div $SELECTOR$></div>`);
	});

	it('should create', () => {
		expect(spectator.directive).toBeTruthy();
	});
});
```

### Pipe ###
#### (sp + Tab) ####
```
import { createPipeFactory, SpectatorPipe } from '@ngneat/spectator';

import { $NAME$ } from './$PATH$';

describe('$NAME$', () => {
	let spectator: SpectatorPipe<$NAME$>;
	const createPipe = createPipeFactory({
		pipe: $NAME$,
	});

	beforeEach(() => {
		spectator = createPipe(`<div>{{ input | $PIPE$ }}</div>`, {
			hostProps: { input: 'Test' },
		});
	});

	it('should create', () => {
		expect(spectator.element).toBeTruthy();
	});
});
```

