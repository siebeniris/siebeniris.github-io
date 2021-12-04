# Set up M1 Max for Deep Learning

## install xcode and Command Line Tools Package

`xcode-select --install`

## Install Miniforge

* [Miniforge GitHub](https://github.com/conda-forge/miniforge)

* install miniforge for MacOS

   * `brew install miniforge`
   * After this step the conda version is `4.10.3`.

* Create and activate a conda environment with python>=3.7.
	```
	conda create -n m1 python=3.8
	conda activate m1
	```

## Install Compatible Pytorch

* Reference: [Use IOS GPU in Pytorch](https://pytorch.org/tutorials/prototype/ios_gpu_workflow.html#metal-compatible-model)

* Install a pytorch nightly that includes the `Meta` backend

	`pip3 install --pre torch -f https://download.pytorch.org/whl/nightly/cpu/torch_nightly.html`


* check the torch version, to this day, it is `1.11.0.dev20211203`.

```
import torch
torch.__version__
```

* install torchtext `pip install torchtext`


* Try out the following scripts, see more in the reference.

```
import torch
import torchvision
from torch.utils.mobile_optimizer import optimize_for_mobile

model = torchvision.models.mobilenet_v2(pretrained=True)
scripted_model = torch.jit.script(model)
optimized_model = optimize_for_mobile(scripted_model, backend='metal')
print(torch.jit.export_opnames(optimized_model))
optimized_model._save_for_lite_interpreter('./mobilenetv2_metal.pt')
```

## Install Tensorflow 

* Reference: [Getting Started with tensorflow-metal PluggableDevice](https://developer.apple.com/metal/tensorflow-plugin/https://developer.apple.com/metal/tensorflow-plugin/)

* `conda install -c apple tensorflow-deps`, after this step, the conda version is `4.11.0`.
* `conda install tensorflow-macos`
* `conda install tensorflow-metal`


## Install Tranformers
* Reference: [Install Hugging Face Transformers on Apple M1](https://towardsdatascience.com/hugging-face-transformers-on-apple-m1-26f0705874d7)

* Install Tokenizers 
	* install Rust Language
		 `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`
	* install tokenizsers
		`git clone https://github.com/huggingface/tokenizers`
	* Go to the python bindings folder `cd tokenizers/bindings/python`
	* `pip install setuptools_rust`
	* `python setup.py install`

* Install transformers
	`pip install git+https://github.com/huggingface/transformers`


## Asitop

Kudos :heart: to [Timothy Liu](https://github.com/tlkh), [asitop](https://github.com/tlkh/asitop) is very nice and easy to use- `Performance monitoring CLI tool for Apple Silicon`.

* `pip install asitop`

* `sudo asitop`


## Benchmarking

* Reference: [Apple-M1-BERT Inference](https://github.com/octoml/Apple-M1-BERT)


* Look into file `run_graphdef.py` and `run_keras.py`, instead of using `mlcompute`, insert the following code to setting up `gpu` from M1 Max:

```
gpus = tf.config.experimental.list_physical_devices('GPU')
    if len(gpus) > 0 : print('Available GPUs: {}'.format(gpus))
    else             : print('!!! No GPUs found !!!')
    if device =="gpu":

        device = gpus[0]
    elif device=="cpu":
        device= tf.config.experimental.list_physical_devices('CPU')[0]
    print('device:' , device)

```

* Following the instruction for benchmarking [https://github.com/octoml/Apple-M1-BERT#benchmark-apple-tensorflow-with-mlcompute](https://github.com/octoml/Apple-M1-BERT#benchmark-apple-tensorflow-with-mlcompute)

* M1 Max (2021):
	* CPU benchmark from `python run_keras.py --device cpu`, output: 

	```
	[Keras] Mean Inference time (std dev) on 
	PhysicalDevice(name='/physical_device:CPU:0', device_type='CPU'): 
	51.705708503723145 ms (8.090285680405165 ms)
	```
    

    * GPU benchmark from `python run_keras.py --device gpu`, output: 

	```
	[Keras] Mean Inference time (std dev) on 
	PhysicalDevice(name='/physical_device:GPU:0', device_type='GPU'): 
	51.065993309020996 ms (7.654708410313706 ms)
	```

	* CPU benchmark from `python run_graphdef.py --device cpu --graph-path ./models/bert-base-uncased.pb`

	```
	[Graphdef] Mean Inference time (std dev) on 
	PhysicalDevice(name='/physical_device:CPU:0', device_type='CPU'): 
	18.558645248413086 ms (2.615691413527943 ms)
	```


	* GPU benchmark from `python run_graphdef.py --device gpu --graph-path ./models/bert-base-uncased.pb.`

	```
	[Graphdef] Mean Inference time (std dev) on 
	PhysicalDevice(name='/physical_device:GPU:0', device_type='GPU'):
	18.612136840820312 ms (1.4835947730761807 ms)
	```

The performance is much improved compared to the M1 (2020), see the reference.

