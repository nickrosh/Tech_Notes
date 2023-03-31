
## Tensor


## Dataset & DataLoader

PyTorch provides two data primitives: `torch.utils.data.DataLoader` and `torch.utils.data.Dataset` that allow you to use pre-loaded datasets as well as your own data. `Dataset` stores the samples and their corresponding labels, and the `DataLoader` wraps an iterable around `Dataset` to enable easy access to the samples.

When you want to create a custom dataset, there are three functions that must be implemented. `__init__` is run once to instantiate the dataset object. We initialize the locations of the data, the labels, and the transforms if specified. The `__len__` simply returns the number of samples in our dataset. The `__getitem__` function loads and returns a sample from the dataset at the given `index`.

```python
from torch.utils.data import Dataset

class CustomImageDataset(Dataset):
	def __init__(self, data_dir, labels, transform=None, target_transform=None):
		self.data_dir = data_dir
		self.labels = labels
		self.transform = transform
		self.target_transform = target_transform

	def __len__(self):
		return len(self.labels)

	def __getitem__(self, index):
		# logic to fetch a particular piece of data
		if self.transform:
			data = self.transform(data)
		if self.target_transform:
			label = self.target_transform(label)
		return data, label
```

The `DataLoader` is an iterable that we can use to abstract the functions of iterating through the dataset in parallel and in batches. You simply pass in the dataset, the [batch size](../../Machine%20Learning/Deep%20Learning/Optimizers.md) and any other parameters like shuffling after every epoch.

## Torch.nn

While we have the ability to manually set the forward and backprop step of every layer of a model, PyTorch provides a higher level API in the form of `torch.nn`. This method allows you to define roughly the "layers" of your [Neural Networks](../../Machine%20Learning/Deep%20Learning/Neural%20Networks.md). You can also define whatever [Activation Functions](../../Machine%20Learning/Deep%20Learning/Activation%20Functions.md) you want, as well as any other special type of layer like [Convolutional layers](../../Machine%20Learning/Deep%20Learning/Convolutional%20Neural%20Net.md). Here is a 3 layer ConvNet:

```python
model = nn.Sequential(
    nn.Conv2d(1, 16, kernel_size=3, stride=2, padding=1),
    nn.ReLU(),
    nn.Conv2d(16, 16, kernel_size=3, stride=2, padding=1),
    nn.ReLU(),
    nn.Conv2d(16, 10, kernel_size=3, stride=2, padding=1),
    nn.ReLU(),
    nn.AdaptiveAvgPool2d(1),
    Lambda(lambda x: x.view(x.size(0), -1)),
)
```
