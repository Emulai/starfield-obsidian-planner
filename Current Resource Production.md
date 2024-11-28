Organic:: 
Inorganic:: [[Aluminium]], [[Iron]], [[Helium-3]], [[Argon]], [[Water]], [[Beryllium]], [[Titanium]], [[Tungsten]], [[Lead]]
Fabricated:: [[Adaptive Frame]]

```dataviewjs
const organicIn = dv.current().organic;
const inorganicIn = dv.current().inorganic;
const fabricatedIn = dv.current().fabricated;
const producedResources = [];

function resourceToArray(input) {
	if (!input) return [];
	if (!Array.isArray(input)) return [input];

	return input;
}

function getType(folder) {
	const pathSections = folder.split('/');
	return pathSections[pathSections.length - 1];
}

function sort(a, b) {
	var nameA = a.file.name.toUpperCase();
	var nameB = b.file.name.toUpperCase();
	return (nameA < nameB) ? -1 : (nameA > nameB) ? 1 : 0;
}

producedResources.push(...resourceToArray(organicIn));
producedResources.push(...resourceToArray(inorganicIn));
producedResources.push(...resourceToArray(fabricatedIn));

const allOrganics = [...dv.pages('"resources/organic"')];
const allInorganics = [...dv.pages('"resources/inorganic"')];
const allFabricated = [...dv.pages('"resources/fabricated"')];

producedResources.forEach(pr => {
	const resource = dv.page(pr.path);
	const organicIndex = allOrganics
		.findIndex(ao => ao.file.name === resource.file.name);
	const inorganicIndex = allInorganics
		.findIndex(ai => ai.file.name === resource.file.name);
	const fabricatedIndex = allFabricated
		.findIndex(af => af.file.name === resource.file.name);

	if (organicIndex >= 0)
		allOrganics.splice(organicIndex, 1);
	if (inorganicIndex >= 0) 
		allInorganics.splice(inorganicIndex, 1);
	if (fabricatedIndex >= 0) 
		allFabricated.splice(fabricatedIndex, 1);
});

dv.paragraph(`#### Inorganics ${allInorganics.length}`);
dv.table(['Name', 'Type'], allInorganics
	.sort(sort)
	.map(ai => [ai.file.name, getType(ai.file.folder)]));

dv.paragraph(`#### Fabricated ${allFabricated.length}`);
dv.table(['Name', 'Type'], allFabricated
	.sort(sort)
	.map(af => [af.file.name, getType(af.file.folder)]));

dv.paragraph(`#### Organics ${allOrganics.length}`);
dv.table(['Name', 'Type'], allOrganics
	.sort(sort)
	.map(ao => [ao.file.name, getType(ao.file.folder)]));
```

