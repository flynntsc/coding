```
// 读文件
const fs = require('fs');

var readPackageFile = new Promise(function(resolve, reject) {
    fs.readFile('./package.json', function(err, data) {
        if (err) {
            reject(err);
        }

        resolve(data);
    });
});
// 路径是否存在
var checkBackupDir = new Promise(function(resolve, reject) {
    fs.exists('./backup', function(exists) {
        if (!exists) {
            resolve(mkBackupDir);
        } else {
            resolve();
        }
    });
});
// 创建路径
var mkBackupDir = new Promise(function(resolve, reject) {
    // throw new Error('unexpected error');
    fs.mkdir('./backup', function(err) {
        if (err) {
            return reject(err);
        }
        resolve();
    });
});
// 写入文件，因为要带data，所以用函数声明写法
function backupPackageFile(data) {
    return new Promise(function(resolve, reject) {
        fs.writeFile('./backup/package.json', data, function(err) {
            if (err) {
                return reject(err);
            }
            resolve();
        });
    });
};

// 1. 读取当前目录的package.json
readPackageFile.then(checkBackupDir)
    .then(backupPackageFile)
    .then(function(val) {
        console.log('backup successed');
    }).catch(function(err) {
        console.error('cuowu' + err);
    });
    ```
