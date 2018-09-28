# -清理某路径下的远程备份文件


#!/usr/bin/env python
#coding=utf-8

import os
import time
import sys

def listFile(path):
    fList = []
    fList1 = os.listdir(path)
    for f1 in fList1:
        if os.path.isfile(os.path.join(path, f1)):
            fList.append(os.path.join(path,f1))
        elif os.path.isdir(os.path.join(path, f1)):
            fList += listFile(os.path.join(path, f1))
        else :
            continue
    return fList

def removeOuttimeFile(file,dNum):
    mTime = os.stat(file).st_mtime
    curTime = time.time()
    if mTime < curTime - 3600*24*dNum:
        return True,file
    return False,file
def main(path, dNum):

    if not os.path.isfile(logFile):
        with open(logFile, "w") as fobj:
            fobj.write("")

    fList = listFile(path)
    obj_l = []
    try:
        for f in fList:
            status, fname = removeOuttimeFile(f, dNum)
            if status:
                os.remove(f)
                with open(logFile, "a") as fobj:
                    fobj.write("%s :文件 %s 超过 %s 天未作修改，已删除。\n" % (time.strftime("%Y-%m-%d %H:%M:%S"),fname, dNum))
    except:
        return False
    return True

if __name__ == "__main__":
    logFile = os.path.join(os.path.dirname(os.path.abspath(__file__)), "delRecords.log")            #脚本执行结果日志
    ret = main("/data/backup", 5)                                   #清理该路径下的文件（包括其无限子目录），过期日期5天
    if not ret:
        with open(logFile, "a") as fobj:
            fobj.write("%s :执行删除长期未修改文件动作失败。\n" % time.strftime("%Y-%m-%d %H:%M:%S"))
        sys.exit(1)
    sys.exit(0)
