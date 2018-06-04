# httprunner_study#unittest单元测试框架了解
基础用法

exapmle

    import unittest
    class TestStringMethods(unittest.TestCase):

	   def test_upper(self):
	    	self.assertEqual("foo".upper(), "FOO")
	   def test_isupper(self):
		    self.assertTrue('FOO'.isupper())
		    self.assertFalse('Foo'.isupper())

	   def test_split(self):
	 	   s = 'hello world'
	 	   self.assertEqual(s.split(), ['hello', 'world'])
	 	   self.assertEqual(s.split(), ['hello', 'world'])
	 	   with self.assertRaises(TypeError):
	 		   s.split(2)

    if __name__ == '__main__':
	   unittest.main()

加上-v,会实现详细的方法报告

    localhost:Downloads guohongxing$ python3 1.py -v
    test_isupper (__main__.TestStringMethods) ... ok
    test_split (__main__.TestStringMethods) ... ok
    test_upper (__main__.TestStringMethods) ... ok
运行的时候，也可以加入一些选项：

    python -m unittest --f 1.py
    命令包括：
    -b --buffer
    -c -- catch
    -f --failfast
    --locals
    

##测试覆盖 test discovery
主要针对文件名是test.py的文件/某个文件目录下面的单元测试文件，可以使用命令

    cd project_directory
    python -m unittest discover

可以使用命令



   
