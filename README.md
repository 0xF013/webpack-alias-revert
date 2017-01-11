const fs = require('fs');
const path = require('path');

const aliases = {
  actions: 'redux/actions',
  reducers: 'redux/reducers',
  constants: 'constants'
};

function getStepsToSrc(file) {
  const slashCount = (file.match(/\//g) || []).length;
  console.log('slashcount for ', file, ' ', slashCount);
  if (slashCount === 2) {
    return './';
  } else {
    return '../'.repeat(slashCount - 2);
  }
}

function processFile(file, aliases) {
  fs.readFile(file, 'utf8', function(err, data) {
  if (err) {
    return console.log(err);
  }
  var result = data;
  console.log('path: ', file);
  Object.keys(aliases).forEach(alias => {
    console.log('path: ', file);
    console.log('alias: ', alias);
    console.log('regexp: ', `from '${alias}`);
    const stepsToSrc = getStepsToSrc(file);
    result = result.replace(new RegExp(`from '${alias}`, 'g'), `from '${stepsToSrc}` + aliases[alias]);
  });

  fs.writeFile(file, result, 'utf8', function(err) {
     if (err) return console.log(err);
  });
});
}

const testFolder = './src/';
const jsExtensions = ['.js', '.jsx'];
function processDirectory(folder) {
  console.log('---', folder);
  fs.readdir(folder, (err, files) => {
    files.forEach(file => {
      const filePath = folder + file;
      if (fs.lstatSync(filePath).isDirectory()) {
        console.log('directory: ', file);
        processDirectory(filePath + '/');
      } else {
        if (jsExtensions.includes(path.extname(file))) {
          console.log('file: ', file);
          processFile(filePath, aliases);
        }
      }
    });
  })
}

processDirectory(testFolder);
